# Jeedom Install
Please install Jeedom + MQTT Manager + Ajax System (8 euro one time in Jeedom Market, and definitely worth it).

# Jeedom Configuration
It is mandatory to apply the code patch and configure Jeedom before configuring the Home Assistant integration.

Open Settings
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/system_config.png)

# External Access
It is ABSOLUTELY necessary that your Jeedom be accessible from the outside (using the external access URL).
Setup it here. 
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/ext_url.png)

I use a WireGuard tunnel (WireGuard client addon) from my HA to my server with a public IP. The MQTT client connects to the WireGuard IP to push updates to the HA MQTT server. Or you can use port forwarding to open external access to an MQTT server behind NAT.

# Jeedom Path

We need to patch Jeedom code to access all functions in the Ajax API. From Jeedom we use only two functions:
- ajaxSystem::request to call Ajax API via Jeedom Market gateway
- redirect events from Ajax API to mqtt, before Jeedom will parse it.

Jeedom is used to log in first and manage tokens to access the Ajax API. It is possible to rewrite `ajaxSystem::request` locally and not use Jeedom at all. If you want, you can help with that. It is not a lot of code, but it would need testing. I imagine it as a small HA addon that listens on a public URL for Ajax API events and redirects everything to MQTT.

1. Open the built-in Code Editor
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/editor.png)

2. Locate File /plugins/ajaxSystem/core/php/jeeAjaxSystem.php
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/ajaxphp.png)

3. Copy and paste the selected code and save it just after `Log::Add`
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/path.png)

```php
if (class_exists('mqtt2', true)) {
  if (isset($datas['ha_direct_call'])) {
   	$func = $datas['ha_direct_call'];
     
    if ($func=='get_userId') {
    	$data = ['userId' => config::byKey('userId', 'ajaxSystem'), 'test'=>'ok'];
      	echo json_encode($data);
    	http_response_code(200);
 		die();
    } elseif ($func=='AjaxApi') {
    	$ajax_path = $datas['a_path'];
    	$ajax_data = $datas['a_data'];
    	$ajax_type = $datas['a_type'];    
      	try {
        	$rr = ajaxSystem::request($ajax_path, $ajax_data, $ajax_type);
          	$r = ['result' => $rr];
      	}
        catch(Exception $e) {
          	$r = ['exception' => $e->getMessage()];
        }
      	echo json_encode($r);
      
      	http_response_code(200);
 		die();
    }
    
    http_response_code(500);
 	die();
    
  } else {
  	mqtt2::publish('jeedom/raw/event', json_encode($datas));
  }
}
```

# Ajax API Key
To set up the Home Assistant integration you need:
- External Access URL
- This Ajax API key
 

![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/ajax_api.png)

# Updates
It's better to disable everything
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/updates1.png)

And for plugin
![image](https://github.com/CommanderTux/ha_ajax_security/blob/main/jeedom/updates2.png)




