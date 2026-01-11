```c#
var builder = WebApplication.CreateBuilder(args);

builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, config =>
    {
        config.RequireHttpsMetadata = false;
        config.SaveToken = true;
        config.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey("ByteAarray_JwtKey1"),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    })
    .AddJwtBearer("ExternalAPI", config =>
    {
        config.RequireHttpsMetadata = false;
        config.SaveToken = true;
        config.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey("ByteAarray_JwtKey2"),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    });

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.Run();
```


```c#
[ApiController]
[Route("[controller]")]
public class AuthenController : ControllerBase
{
    [HttpGet("FakeToken")]
    public IActionResult FakeToken()
    {
        var tokenHandler = new JwtSecurityTokenHandler();
        var cliamsValues = new Dictionary<string, string>()
        {
            { "UserId", "U123" },
            { "Email", "U123@mail.com" }
        };
        var claims = cliamsValues
          .Select(keyvalue => new Claim(keyvalue.Key, keyvalue.Value))
          .ToArray();
        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(claims),
            Expires = DateTime.UtcNow.AddMinutes(30),
            SigningCredentials = new SigningCredentials(
                new SymmetricSecurityKey("ByteAarray_JwtKey1 | ByteAarray_JwtKey2"),
                SecurityAlgorithms.HmacSha256Signature
            )
        };
        var securityToken = tokenHandler.CreateToken(tokenDescriptor);
        var token = tokenHandler.WriteToken(securityToken);
        return Ok(token);
    }
```

```c#
    [ApiController]
    [Route("[controller]")]
    public class UserController : ControllerBase
    {
        [HttpGet]
        [Authorize(AuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
        public IActionResult Get()
        {
            return Ok("user can accessd");
        }

        [HttpGet("ExternalAPI")]
        [Authorize(AuthenticationSchemes = "ExternalAPI")]
        public IActionResult ExternalApiGetUser()
        {
            return Ok("ExternalAp can accessd");
        }
    }
```
