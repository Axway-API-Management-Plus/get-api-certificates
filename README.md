# Get API-Certificates
This policy can be used to access the configured certificates for the called API in a Custom-Policy.

This asset doesn't really contain the policy itself. Instead it contains the important Script code. Ultimately you need to the
following into filters into your policy:  
![Policy snippet](https://github.com/Axway-API-Management-Plus/get-api-certificates/blob/master/misc/images/SampleRequestPolicy.png)
  
The first filter is accessing the KPS to load the CaCertsBlob from the database for the actually called API:  
![Get Certs from KPS](https://github.com/Axway-API-Management-Plus/get-api-certificates/blob/master/misc/images/AccessFEAPICaCerts.png)

As a second step add a Scripting filter with the folowing code:
```groovy
import java.util.Base64;
import java.nio.charset.StandardCharsets;
import com.fasterxml.jackson.core.type.TypeReference;
import com.vordel.apiportal.api.portal.model.proxy.CACert;
import com.vordel.trace.Trace;

def invoke(msg)
{
        def mapper = new com.fasterxml.jackson.databind.ObjectMapper();
        def ls = java.lang.System.lineSeparator();
        def data = msg.get("caCertsBlob");
        data = data.replaceAll(ls, "");
        def base64DecodedData = Base64.getDecoder().decode(data);
        def caCertsJson = new java.lang.String(base64DecodedData, StandardCharsets.UTF_8);
        def caCerts = mapper.readValue(caCertsJson, new TypeReference<List<CACert>>() {});
        msg.put("caCerts", caCerts);
        return true;
}
```
The scripting should look like: 

![Get Certs from KPS](https://github.com/Axway-API-Management-Plus/get-api-certificates/blob/master/misc/images/DecodeCertificates.png)  

This gives you a List\<CACert\> attribute under the key: `caCerts`, which you can use to iterate.  
  
Each `CaCert` provide the following fields:  

| Field              | Summary               | Sample |
| :---               | :---                  | :---   |
| alias              | Certificate alias                  | CN=Equifax Secure eBusiness CA-1, O=Equifax Secure Inc., C=US  |
| subject            | Certificate subject                  | CN=Equifax Secure eBusiness CA-1, O=Equifax Secure Inc., C=US  |
| issuer             | Certificate issuer                  | CN=Equifax Secure eBusiness CA-1, O=Equifax Secure Inc., C=US  |
| version            | Version of the certificate                  | 3  |
| notValidBefore     | Start date of the certificate                  | 1364287486876  |
| notValidAfter      | Expiry date of the certificate                  | 1364287486876  |
| signatureAlgorithm | The algorithm used to sign the certificate                  | RSA (2048 bits)  |
| expired            | Flag indicating whether or not the certificate is expired                  | true  |
| notYetValid        | Flag indicating whether or not the certificate is valid yet.                  | false  |
| inbound            | Flag indicating whether this certificate is used for inbound SSL connections when invoking the virtualized API.                  | false  |
| outbound           | Flag indicating whether this certificate is used for outbound SSL connections when invoking the virtualized API.                  | true  |

## Limitations/Caveats
Nothing known

## Contributing

Please read [Contributing.md](https://github.com/Axway-API-Management-Plus/Common/blob/master/Contributing.md) for details on our code of conduct, and the process for submitting pull requests to us.  

## Team

![alt text][Axwaylogo] Axway Team

[Axwaylogo]: https://github.com/Axway-API-Management/Common/blob/master/img/AxwayLogoSmall.png  "Axway logo"


## License
[Apache License 2.0](/LICENSE)
