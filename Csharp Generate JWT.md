Write a simple class called MainClass with a method string GenerateJwtWithFixedClaims(string secret, string issuer, string audience, string sub, string jti, long iat) that takes a secret, issuer, audience, subject, JWT ID, and issued at time as parameters and generates a JWT with the following claims:

1. sub (subject) with the provided value.
2. jti (JWT ID) with the provided value.
3. iat (issued at) with the provided Unix timestamp value.

The JWT should not have an expiration claim and should be signed using the HMAC SHA-256 algorithm.

Example Input:
`GenerateJwtWithFixedClaims("your-secret-key-1234", "your-issuer", "your-audience", "sub-value-1", "jti-value-1", 1626300000);`

Example Output:
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJzdWItdmFsdWUtMSIsImp0aSI6Imp0aS12YWx1ZS0xIiwiaWF0IjoxNjI2MzAwMDAwLCJpc3MiOiJ5b3VyLWlzc3VlciIsImF1ZCI6InlvdXItYXVkaWVuY2UifQ.MLB4TiTE40ps89RPesxASz0SzUMe_i3NDmmykZiv1ps`

------

MyCode:

```
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.IdentityModel.Tokens;

class MainClass {

  public static string GenerateJwtWithFixedClaims(string secret, string issuer, string audience, string sub, string jti, long iat) {
    var signature = new SigningCredentials(
      new SymmetricSecurityKey(
        Encoding.UTF8.GetBytes(secret)
      ),
      SecurityAlgorithms.HmacSha256
    );

    var claims = new System.Collections.Generic.List<Claim>(){
      new Claim("sub", sub),
      new Claim("jti", jti),
      new Claim("iat", "" + iat, ClaimValueTypes.Integer)
    };

    var jwtToken = new JwtSecurityToken(
      issuer: issuer,
      audience: audience,
      claims: claims,
      signingCredentials: signature
    );

    return new JwtSecurityTokenHandler().WriteToken(jwtToken);

  }

  static void Main() {
    Console.WriteLine(
      GenerateJwtWithFixedClaims(
        "your-secret-key-1234", 
        "your-issuer", 
        "your-audience", 
        "sub-value-1", 
        "jti-value-1", 
        1626300000
      )
    );
  }
  
}
```
