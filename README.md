[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/WZovCNvQ)
## Bibliografia
📎 Como leer el name de un toquen jwt para poder usarlo en un cliente en .net razor pages
> En .NET Razor Pages, puedes leer el "name" de un token JWT utilizando la biblioteca System.IdentityModel.Tokens.Jwt. Aquí te dejo un ejemplo de cómo hacerlo:

csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Security.Claims;

public class JwtHelper
{
    public static string GetNameFromJwt(string jwtToken)
    {
        var handler = new JwtSecurityTokenHandler();
        var token = handler.ReadJwtToken(jwtToken);

        var nameClaim = token.Claims.FirstOrDefault(c => c.Type == ClaimTypes.Name);
        return nameClaim?.Value ?? "Nombre no encontrado";
    }
}
En este código:

Se usa JwtSecurityTokenHandler para leer el token.

Se obtiene el claim del tipo ClaimTypes.Name, que es donde suele guardarse el nombre de usuario.

Si el claim no está presente, devuelve "Nombre no encontrado".

Si el token viene en el contexto de una autenticación en Razor Pages, puedes acceder al usuario autenticado con HttpContext.User:

csharp
var userName = User.Identity.Name;
Esto asume que el token ya ha sido validado y asignado al contexto de autenticación.

¿Te gustaría que adaptáramos esto a un caso más específico? 🚀
📎 Como hacer que en una api el usuario autentificado con un token pueda hacer que en un metodo de pille su name del token en c#
> Para obtener el nombre del usuario desde un token en una API en C#, generalmente se usa la autenticación basada en JWT (JSON Web Token). Aquí tienes un enfoque utilizando ASP.NET Core:

Extraer el nombre desde el ClaimsPrincipal: Cuando el usuario se autentica, su información se almacena en los claims del token.

Acceder a los claims en un controlador:

csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

[Authorize] // Asegura que solo usuarios autenticados puedan acceder
[ApiController]
[Route("api/[controller]")]
public class UsuarioController : ControllerBase
{
    [HttpGet("nombre")]
    public IActionResult ObtenerNombreUsuario()
    {
        // Obtiene el nombre del usuario desde los claims del token
        var nombreUsuario = User.FindFirst(ClaimTypes.Name)?.Value;

        if (string.IsNullOrEmpty(nombreUsuario))
        {
            return Unauthorized("No se pudo obtener el nombre del usuario.");
        }

        return Ok(new { Nombre = nombreUsuario });
    }
}
¿Cómo funciona?
La propiedad User en los controladores de ASP.NET Core contiene los claims extraídos del token.

FindFirst(ClaimTypes.Name)?.Value obtiene el nombre del usuario si fue incluido en el token.

La anotación [Authorize] asegura que solo usuarios autenticados puedan acceder a la ruta.

Si el nombre del usuario no está en ClaimTypes.Name, verifica cómo se generó el token y qué claims incluye. ¡Avísame si necesitas más detalles! 🚀
📎 como usar un metodo de una api que tenga [authorize] desde un cliente web en c#
Para llamar a un método de una API que tenga [Authorize] desde un cliente web en C#, debes asegurarte de que tu solicitud incluya un token de autenticación válido. Aquí te dejo los pasos clave para hacerlo:

1. Obtener un token de acceso
Si la API utiliza OAuth2, OpenID Connect o algún otro método de autenticación basado en tokens, primero necesitas obtener un token de acceso desde el proveedor de autenticación.

csharp
var client = new HttpClient();
var tokenRequest = new HttpRequestMessage(HttpMethod.Post, "https://tu-auth-server.com/token")
{
    Content = new FormUrlEncodedContent(new Dictionary<string, string>
    {
        { "grant_type", "client_credentials" },
        { "client_id", "tu-client-id" },
        { "client_secret", "tu-client-secret" }
    })
};

var response = await client.SendAsync(tokenRequest);
var tokenResponse = await response.Content.ReadAsStringAsync();
var token = JsonConvert.DeserializeObject<TokenResponse>(tokenResponse).access_token;
2. Usar el token en la solicitud a la API
Una vez que tengas el token, puedes agregarlo a las cabeceras de tu solicitud para autenticarte correctamente.

csharp
var apiClient = new HttpClient();
apiClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);

var apiResponse = await apiClient.GetAsync("https://tu-api.com/recurso-protegido");
var contenido = await apiResponse.Content.ReadAsStringAsync();

Console.WriteLine(contenido);
Si necesitas manejar autenticación con el usuario (por ejemplo, mediante login y contraseña), podrías usar AuthorizationCodeFlow en lugar de client_credentials, dependiendo de cómo esté configurado el servidor de autenticación.

¿Tienes algún detalle específico sobre la API que estés usando? Puedo ajustar la respuesta a tu escenario exacto. 🚀
