# se-leg proofing flow

1. The user logs in to the RP/IdP with two-factor authentication.
2. When logged in, the user can initiate the proofing process with the RP/IdP.
3. The user initiates the vetting process.
4. The RP/IdP sends an OpenID Connect authentication request using the
   [`Authorization Code Flow`](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth).

   The request MUST be sent using HTTP POST and MUST contain a nonce.

   An example request:
   ```
   POST /authorize HTTP/1.1
   Host: demo.se-leg
   Content-Type: application/x-www-form-urlencoded

   response_type=code
     &scope=openid
     &client_id=s6BhdRkqt3
     &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
     &nonce=n-0S6_WzA2Mj
   ```
5. If the authentication request is registered by the OP, a `200 OK` is
   returned.
6. The RP/IdP MUST present the generated nonce as a QR code, which the user will
   bring to the RA.
7. If the authentication request is incorrect, a `4XX` HTTP error code is
   returned.
8. The RP/IdP should present an error message to user.
9. The user presents the QR code, either printed or on a mobile device, and a 
   identity document to the RA.
10. The RA registers the nonce encoded in the QR code together with the identity
    information from the identity document with the OP.
11. If the vetted identity was registered by the OP, a `200 OK` is returned.
12. If the vetted identity is not registered (e.g. if the nonce presented by
    the user is incorrect) an error will be presented to the RA.
13. The RA asks the user to reinitiate the vetting process at the RP/IdP.
14. The vetted identity is returned to the RP/IdP in as an OpenID Connect
    authentication response.

    An example response:
    ```
    HTTP/1.1 302 Found
    Location: https://client.example.org/cb?
    code=SplxlOBeZQQYbYS6WxSbIA
    &state=af0ifjsldkj
    ```
15. To get additional user information, the RP/IdP can use the obtained code
    to make an [OpenID Connect UserInfo Request](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest).
16. The OP will send an [OpenID Connect UserInfo Response](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoResponse).


If the nonce from the vetting app is invalid (unknown, or otherwise incorrect).
