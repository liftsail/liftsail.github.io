> 
>
> # ****
>
> 什么是 JWT？

JSON Web Token，通过数字签名的方式，以 JSON 对象为载体，在不同的服务终端之间安全的传输信息。



> JWT 有什么用？

JWT 最常见的场景就是授权认证，一旦用户登录，后续每个请求都将包含JWT，系统在每次处理用户请求的之前，都要先进行 JWT 安全校验，通过之后再进行处理。



> JWT 的组成

JWT 由 3 部分组成，用.拼接

```java
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IlRvbSIsInJvbGUiOiJhZG1pbiIsInN1YiI6ImFkbWluLXRlc3QiLCJleHAiOjE2MjMyMjM2NzUsImp0aSI6ImQ2MTJjZjcxLWI5ZmUtNGMwNy04MzQwLTViOWViZmMyNjExNyJ9.FOS9Y7rYNdc2AOidnSPrgg2XTYePU0yGZ598h2gtabE
```

这三部分分别是：

- Header

  ```java
  {
    'typ': 'JWT',
    'alg': 'HS256'
  }
  ```

- Payload

  ```java
{
      "sub": '1234567890',
      "name": 'john',
      "admin":true
}
  ```

- Signature


```javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret');
```



pom.xml

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>

<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>
```

**代码**

```java
import io.jsonwebtoken.*;

import java.util.Date;
import java.util.UUID;

public class Test {

    private long time = 1000*60*60*24;
    private String signature = "admin";

    @org.junit.Test
    public void jwt(){
        JwtBuilder jwtBuilder = Jwts.builder();
        String jwtToken = jwtBuilder
                //header
                .setHeaderParam("typ", "JWT")
                .setHeaderParam("alg", "HS256")
                //payload
                .claim("username", "tom")
                .claim("role", "admin")
                .setSubject("admin-test")
                .setExpiration(new Date(System.currentTimeMillis()+time))
                .setId(UUID.randomUUID().toString())
                //signature
                .signWith(SignatureAlgorithm.HS256, signature)
                .compact();
        System.out.println(jwtToken);
    }

    @org.junit.Test
    public void parse(){
        String token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InRvbSIsInJvbGUiOiJhZG1pbiIsInN1YiI6ImFkbWluLXRlc3QiLCJleHAiOjE2MjMyMjY2NDEsImp0aSI6IjAzNDUwYzQ2LWIwZGUtNDcxNy1hZWZmLTNjNDkzMDJlYzEyOSJ9.e7fX0VRSTdb5suRpV5Z-h_mkXuniqbK1-zCsxLU1vuA";
        JwtParser jwtParser = Jwts.parser();
        Jws<Claims> claimsJws = jwtParser.setSigningKey(signature).parseClaimsJws(token);
        Claims claims = claimsJws.getBody();
        System.out.println(claims.get("username"));
        System.out.println(claims.get("role"));
        System.out.println(claims.getId());
        System.out.println(claims.getSubject());
        System.out.println(claims.getExpiration());
    }

}
```
