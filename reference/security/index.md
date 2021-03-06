---

copyright:
  years: 2016, 2018
lastupdated: "2018-01-11"

---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}


# {{site.data.keyword.iot_short_notm}} security
{: #sec-index}

As a cloud-hosted service the {{site.data.keyword.iot_full}} embeds security as an important aspect of its architecture.
{: shortdesc}

The following document answers some common questions about how your organization's data is protected, focusing on specific areas:

* Compliance: external standards which set benchmarks for security.
* Authentication: assuring the identity of users, devices or applications that are attempting to access your organization's information.
* Authorization: assuring that users, devices and applications have permission to access your organization's information.
* Encryption: assuring that data is only readable by authorized parties only and cannot be intercepted.

## {{site.data.keyword.iot_short_notm}} and {{site.data.keyword.Bluemix_notm}}
{: #iot-bluemix-sec}

{{site.data.keyword.iot_short_notm}} runs within {{site.data.keyword.Bluemix_notm}} platform and so relies upon both {{site.data.keyword.Bluemix_notm}} and {{site.data.keyword.BluSoftlayer_notm}} Infrastructure for access and connectivity. The reliance upon {{site.data.keyword.Bluemix_notm}} and {{site.data.keyword.BluSoftlayer_notm}} Infrastructure makes {{site.data.keyword.Bluemix_notm}} and {{site.data.keyword.BluSoftlayer_notm}} Infrastructure security and reliability important to users of {{site.data.keyword.iot_short_notm}}

For more details about the security of the {{site.data.keyword.Bluemix_notm}}, see [{{site.data.keyword.Bluemix_notm}} platform security](index.html#platform-security). 

## {{site.data.keyword.iot_short_notm}} Security compliance
{: #compliance}  
![ISO 27K icon](../../images/icon_iso27k1.png "ISO 27K icon")   
{{site.data.keyword.iot_short_notm}} is certified under the International Organization for Standardization (ISO) 27001 standard, which defines the best practices for information security management processes. The ISO 27001 standard specifies the requirements for establishing, implementing, and documenting Information Security Management Systems (ISMS) and the requirements for implementing security controls, according to the needs of individual organizations. The ISO 27000 family of standards incorporates a process of scaling risk and valuation of assets, with the goal of safeguarding the confidentiality, integrity, and availability of the written, oral, and electronic information.

{{site.data.keyword.iot_short_notm}} is audited by a third-party security firm and meets all of the requirements for ISO 27001: {{site.data.keyword.iot_short_notm}} ISO 27001:2013 Certificate of Registration.


## {{site.data.keyword.iot_short_notm}} Terminology
{: #terminology}

![image](terminology_platform.svg)


## How do we secure IoT information management within your organization?
{: #secure-org}

The browser-based GUI and REST APIs are fronted by HTTPS, with a certificate signed by DigiCert, so you can trust that you're connecting to the genuine {{site.data.keyword.iot_short_notm}}. Access to the web-based GUI is authenticated by your IBMid or {{site.data.keyword.Bluemix_notm}} {{site.data.keyword.ssoshort}}. Using the REST API requires an API key, generated through the GUI, you can use this to make authenticated REST API calls against your organization.

![image](management_platform.svg)


## How do we secure your device and application credentials?
{: #secure-credentials}

When devices are registered or API keys are generated, the authentication token is salted and hashed. This means your organization's credentials can never be recovered from our systems - even in the unlikely event that the {{site.data.keyword.iot_short_notm}} is compromised.

Device credentials and API keys can be individually revoked if they are compromised.

![image](authentication_platform.svg)

## How do we ensure your devices connect securely to the {{site.data.keyword.iot_short_notm}}?
{: #secure-device-connection}

Devices connect by using a clientID and the authentication token that is generated when the devices are added to your platform. MQTT is used to allow simple interoperability across many platforms and languages. The {{site.data.keyword.iot_short_notm}} supports connectivity over TLS v1.2.

**Important:** New organizations are automatically configured to force devices to connect using TLS security by default, which ensures that devices can connect only by using a secure, encrypted channel. However, {{site.data.keyword.iot_short_notm}} also supports cases where organizations must enable devices to connect without TLS. For example, an organization might use devices that lack TLS support, or low-powered IoT devices that cannot spare the processing power necessary to encrypt or decrypt transmissions. The organization's plan determines which settings can be used in these cases.

For more information about how to configure connection security, see [Configuring security policies](set_up_policies.html).

![image](connectivity_platform.svg)


For more information about TLS and cipher suite requirements, see the [TLS requirements](connect_devices_apps_gw.html#tls_requirements) section in the `Application, device, and gateway connections to Watson IoT Platform` documentation.

You can use certificates and security polices to enhance device connection security. Security policies can be set to allow unencrypted connections, to enforce only transport layer security (TLS) connections, and to enable devices to authenticate with client-side certificates and no tokens. Blacklists can be used to specify devices that are not allowed to connect, or whitelists can be used to allow specific devices to connect. For more information about enhanced security, see [Risk and security management](RM_security.html).

### Disabling and enabling devices and gateways
{: #disable-devices}

You can use the **Authorization - Device Management** HTTP API to disable a device from connecting directly to the platform or from connecting behind a gateway. For example, you can forcibly disconnect the device of a malicious user or a device that is not behaving correctly and is causing issues such as unwanted data usage due to spam. The API is used to disconnect the device from its current connection and prevent the device from reconnecting to the platform.

To enable or disable a device, use the following API, where *${clientId}* is the URL-encoded ClientID in the format *d:${orgId}:${typeId}:${deviceId}* for devices or *g:${orgId}:${typeId}:${deviceId}* for gateways:

    PUT /api/v0002/authorization/devices/${clientId}
    
In the request body, use a status value of 0 to disable the device, or use a status value of 1 to enable the device. For example, the following status value indicates that the device is disabled:

    { "status": 0 }

The response code on success is 200. 

When a gateway publishes for a device that is disabled, it receives an error notification with a response code of 180. For more information, see [Gateway notifications](../../gateways/mqtt.html#notification). 

For more information about the API see [Device Security Beta APIs ![External link icon](../../../../icons/launch-glyph.svg "External link icon")](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002-beta/security-subjects-beta.html){:new_window}, and navigate to **Authorization - Device Management**.

## How do we prevent data leaking between IoT devices?
{: #prevent-leak-devices}

Secure messaging patterns are baked in. Once authenticated, devices are only authorized to publish and subscribe to a restricted topic space:

* '/iot-2/evt/<event_id>/fmt/<format_string>'
* '/iot-2/cmd/<command_id>/fmt/<format_string>'

All devices work with the same topic space. The authentication credentials provided by the client dictate to which device this topic space will be scoped by the {{site.data.keyword.iot_short_notm}}.  This prevents devices from being able to impersonate another device.

The only way to impersonate another device is by obtaining compromised security credentials for the device.


![image](device_scope_platform.svg)


Applications can subscribe and publish on both the event and command topics for all devices in the organization. Applications can analyze data from many devices simultaneously, and can also simulate or proxy devices in addition to forming the complementary side of a full duplex communication loop.


## How do we prevent IoT data leaking between organizations?
{: #prevent-leak-org}

The topic space in which devices and applications operate is scoped within a single organization. When authenticated, the {{site.data.keyword.iot_short_notm}} transforms the topic structure using an organization ID based on the client authentication, making it impossible for data from one organization to be accessed from another.

![image](org_scope_platform.svg)

# Related Links
{: #rellinks}
## Related Links
{: #general}
* [Getting started with {{site.data.keyword.iot_short_notm}}](https://console.ng.bluemix.net/docs/services/IoT/index.html)
* [{{site.data.keyword.Bluemix_notm}} security ![External link icon](../../../../icons/launch-glyph.svg "External link icon")](https://console.ng.bluemix.net/docs/security/index.html#security){:new_window}
* [{{site.data.keyword.Bluemix_notm}} platform security ![External link icon](../../../../icons/launch-glyph.svg "External link icon")](https://console.ng.bluemix.net/docs/security/index.html#platform-security){:new_window}
* [{{site.data.keyword.Bluemix_notm}} compliance](https://console.ng.bluemix.net/docs/security/index.html#compliance){:new_window}
* [{{site.data.keyword.BluSoftlayer_notm}} security ![External link icon](../../../../icons/launch-glyph.svg "External link icon")](http://www.softlayer.com/security){:new_window}
* [{{site.data.keyword.BluSoftlayer_notm}} compliance ![External link icon](../../../../icons/launch-glyph.svg "External link icon")](http://www.softlayer.com/compliance){:new_window}
