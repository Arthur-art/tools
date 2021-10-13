# Desenvolvendo uma API Rest com dotnet core
- Curso da desenvolvedor.io que ensina com desenvolver uma api rest com dotnet na prática.

# Documentação do projeto:

# Criando um novo projeto dotnet por linha de comando

- dotnet new

- dotnet new webapi -n ApiDotnet

# Criando com VisualStudio

- Create a new project
- ASP.NET Core Web API
- Target Framework -> .NET 5.0(current)  para integrar com swagger 

<<<<<<< Updated upstream
# Trabalhando com controllers no dotnet

- Uma controller herda de uma classe ControllerBase para lidar apenas com apis rest
- Para demais funcionalidades temos o atributo ApiController para complementar oque a ControllerBase oferece

# Criando a primeira requisição do tipo POST

```c#
using Microsoft.AspNetCore.Mvc;

namespace ApiRestDotnet.Controllers
{
    [ApiController]
    [Route("api-dotnet")]
    public class LearnController : ControllerBase
    {
       
       [HttpPost("sum/result-sum")]
       public ActionResult<string> Get(int num1, int num2)
        {
            int result = num1 + num2;

            return $"{num1} + {num2} é igual à {result}";
        }
    }
}
```
# ActionResults

- Utilizando tipos como ActionResult você consegue ter acesso a métodos como Ok() e BadRquests() entre outros varios métodos afim de tratar suas respostas 
- Tipando o generic do ActionResult você ja prepara o dotnet para saber oque ele irá tratar ou converter
```c#
[HttpGet("return-object")]
        public ActionResult<IEnumerable<string>> GetEnumerableString()
        {
            var valuesObjectString = new string[] {"value1","value2" };

            if(valuesObjectString.Length < 2)
            {
                return BadRequest();
            }

            return Ok(valuesObjectString);
        }
```
# Resgatando dados do corpo da requisicao com atributo FromBody

```c#
[HttpPost("from-body")]
        public ActionResult<string> BodyRequest([FromBody] string name, int age)
        {
            return Ok($"Eu sou o {name} e tenho {age} anos");
        }
```

- Criando uma classe para resgatar um tipo de objeto do corpo da requisição
```c#
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace ApiRestDotnet.Controllers
{
    [ApiController]
    [Route("api-dotnet")]
    public class LearnController : ControllerBase
    {

        [HttpPost("from-body")]
        public ActionResult<string> BodyRequest([FromBody] Product product)
        {
            return Ok(product);
        }
    }

    public class Product
    {
        public Product(int age, string name, string job)
        {
            Age = age;
            Name = name;
            Job = job;
        }

        public int Age { get; set; }

        public string Name { get; set; }

        public string Job { get; set; }
    }
}
```

# Formatadores de dados de resposta personalizados

# Desenvolvendo um ActionResult customizado com uma validação de dados
```c#
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace ApiRestDotnet.Controllers
{
    [Route("format-values-controller")]
    public class FormatValuesController : MainController
    {
       [HttpPost("validate")]
       public ActionResult<string> FormatValues(string name)
        {
            return CustomResponse(name);
        }
    }

    public abstract class MainController : ControllerBase
    {
        protected ActionResult CustomResponse(string name)
        {
            if (OperacaoValida(name))
            {
                return Ok(new 
                {
                    sucess = true,
                    date = name
                });
            }

            return BadRequest(new 
            {
                sucess = false,
                date = "Error"
            });
        }

        [HttpGet]
        public bool OperacaoValida(string result)
        {
            if(result == "Arthur")
            {
                return true;
            }
            return false;
        }
    }
}
```

# CRUD Básico

- Adicionando uma pasta Model na raiz do projeto
# Criando a primeira model - FornecedorViewModel:
```c#
using System;
using System.ComponentModel.DataAnnotations;

namespace ApiRestDotnet.Model
{
    public class FornecedorModel
    {
        [Key]
        public Guid Id { get; set; }
        public string Nome { get; set; }
        public string Documento { get; set; }
        public int TipoFornecedor { get; set; }
        public bool Ativo { get; set; }
    }
}
```

# Trabalhando com EntityFramework para lidar com banco de dados
- Install-Package Microsoft.EntityFrameworkCore.Sqlite
- Install-Package Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite -Version 5.0.10
- Install-Package Microsoft.EntityFrameworkCore.Tools -Version 5.0.10
- Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.10
- Install-Package Microsoft.AspNetCore.Identity.UI -Version 5.0.10
- Install-Package Microsoft.AspNetCore.Identity.EntityFrameworkCore -Version 5.0.10
- Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 5.0.2
- Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 6.0.0-rc.1.21452.10

# Configurando no arquivo Startup
```c#
 public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContext<ApiDbContext>(options => options.UseNpgsql(Configuration.GetConnectionString("DefaultConnection")));

            services.AddControllers();
            services.AddSwaggerGen(c =>
            {
                c.SwaggerDoc("v1", new OpenApiInfo { Title = "ApiRestDotnet", Version = "v1" });
            });
        }
```

# Mapeando a model criando um novo arquivo ApiDbContext
```c#
using Microsoft.EntityFrameworkCore;

namespace ApiRestDotnet.Model
{
    public class ApiDbContext : DbContext
    {
        public ApiDbContext(DbContextOptions options) : base(options)
        {

        }

        public DbSet<FornecedorModel> Fornecedores { get; set; }
    }
}
```

# Configurando arquivo appsettings.json
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=postgres;Username=postgres;Password=123456"
  }
  
}
```
# Configurando Postgres
- https://www.enterprisedb.com/downloads/postgres-postgresql-downloads
- Instalar extension no vstudio -> Npgsql.EntityFrameworkCore.PostgreSQL

# Rodar no terminal nuget para criar a migration:

- Add-Migration Initial
- update-database

# Criando uma controller usando entity framework
- Adicione uma controller com mouse direito na pasta de controllers
- selecione Api Controller with actions, using Entity Framework
=======
# Criando a primeira requisição do tipo get
```c#
 [ApiController]
    [Route("api-dotnet")]
    public class WeatherForecastController : ControllerBase
    {
       
       [HttpGet("obter-id/{id:int}")]
       public ActionResult<string> Get(int id)
        {
            return $"Response => {id}";
        }
    }
```
>>>>>>> Stashed changes
