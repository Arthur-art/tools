# Desenvolvendo um endpoint

- Inicie o projeto abrindo uma solução - micro serviço -  suporte
- Endpoints ficam dentro da pasta controller, basicamente uma classe dentro da camada de API que herda da classe BaseController
- Dentro da pasta controller
  - Add new item - Class - MinhaController
- Esta classe devera ser herdada da classe BaseController dentro do namespace Kernel-API
- Copie uma requisição do tipo POST de algum controller já pronto para o reaproveitamento de código e para manter o Design Pattern
- Na camada de API crie uma pasta Requests para o desenvolvimento de um DTO, um conceito de transferência de dados entre camadas dentro do projeto
- Na pasta Requests adicione uma nova classe chamada - IncludePessoaRequest:

# Arquivo IncludePessoaRequest dentro da pasta Requests na camada de API:

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace CRM.Suporte.API.Requests
{
    //Este record sera necessario para criar uma classe imutavel que sera usada apenas para transferir dados de uma camada para outra
    public record CreatePessoaRequest(
        string Nome,
        int Idade
     );
}
```



- Este record será necessário para criar uma classe que será usada apenas para transferir dados de uma camada para outra
- No arquivo MinhaController adicione esse record como parâmetro do método que ira usa-lo para consumir esses dados
- Dentro do escopo da classe adicione - return Ok("Passei aqui");
- Execute o projeto CRM.Suporte.API junto com docker para verificar se se a solution já esta funcionando
- Para passar pelo Autorized atributo é necessário pegar um token com o postman
- Copiar o idToken para colocar no Authorization do swagger
- Execute o request para verificar o retorno da aplicação


# Arquivo MinhaController na pasta controllers na camada de API:

```c#
using CRM.Kernel.API;
using CRM.Kernel.Application;
using CRM.Suporte.API.Requests;
using CRM.Suporte.Application.Commands.CreatePessoa;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System;   
using System.Threading.Tasks;

namespace CRM.Suporte.API.Controllers
{
    //Atributo Authorize faz com que os actions desta controller precisem de autorização para executar
    [Authorize]
    //O projeto segue um padrão de RestAPI, v1 seria a versão da controller
    [Route("v1/minha-controller")]
    //Classe MinhaController herda da classe BaseController
    public class MinhaController : BaseController
    {


        //Para receber uma requisicao do tipo POST, que ira adicionar um model na base de dados
        //O model tera os seguintes atributos:
        /*
         * Conceito de transferencia de dados entre camadas chamado DTO
         * string nome;
         * int idade;
         */

        [HttpPost]
        //adicionando record como parametro do método que ira usa-lo para consumir os dados
        public async Task<IActionResult> CreatePessoa(CreatePessoaRequest createPessoaRequest)
        {
             return Ok("Passei aqui");

        }

    }
}
```

# 	Desenvolvendo um command

- Na pasta Commands - crie uma nova pasta chamada - CreatePessoa

- A ideia é que o command não tenha lógica de regra de negócio alguma, um command irá apenas orquestrar uma execução de módulos menores do sistema

- Padrão de implementação especifico das classes de um command - CommandHandler - CommandInput - CommandResult - CommandValidator

- Dica de implementação - Entrar em uma pasta de Commands, copiar os 4 arquivos padronizados e colar na pasta do novo command e renomear os arquivos e as classes alterando somente o necessário

- O commandHandler serve para disponibilizar um implementação que vai reagir a um commandInput

- O controller ira gerar um commandInput baseado no request que foi feito no endpoint com os dados da requisição

- Os dados enviados na request são recebidos na Request folder, no CreatePessoaRequest que esta no parâmetro da controller

- Esses dados são passados para a camada de application através de um commandInput

- No Constructor MinhaController sera criado uma variavel commandInput, esse commandInput será enviado para o mediator, e será retornado uma resposta

- Após criar as 4 classes do design pattern do command e tipar corretamente as mesmas, o mediator já sabe que deve chamar o commandHandler e também sabe que ele deve retornar um commandResult

- A camada de API deve conter somente essa implementação, não deve possuir regra de negocio, apenas consumir e retornar dados


# Arquivo MinhaController:

```c#
using CRM.Kernel.API;
using CRM.Kernel.Application;
using CRM.Suporte.API.Requests;
using CRM.Suporte.Application.Commands.CreatePessoa;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System;
using System.Threading.Tasks;

namespace CRM.Suporte.API.Controllers
{
    //Atributo Authorize faz com que os actions desta controller precisem de autorização para executar
    [Authorize]
    //O projeto segue um padrão de RestAPI, v1 seria a versão da controller
    [Route("v1/minha-controller")]
    //Classe MinhaController herda da classe BaseController
    public class MinhaController : BaseController
    {


        //Para receber uma requisicao do tipo POST, que ira adicionar um model na base de dados
        //O model tera os seguintes atributos:
        /*
         * Conceito de transferencia de dados entre camadas chamado DTO
         * string nome;
         * int idade;
         */

        //Referencia do IMediator
        private readonly IMediator _mediator;
        public MinhaController(IMediator mediator)
        {
            //Validando se o mediator não esta como null
            _mediator = mediator ?? throw new ArgumentNullException(nameof(mediator));
        }

        [HttpPost]
        //adicionando record como parametro do método que ira usa-lo para consumir os dados
        public async Task<IActionResult> CreatePessoa(CreatePessoaRequest createPessoaRequest)
        {
            // return Ok("oi");
             
            /*
             * API Layer
             * criar um command input
             * enviar o command input para o mediator
             * retornar a resposta do envio do command
             * 
             * Application Layer
             * Implementar o command handler
             * 
             * Domain Layer
             * Entities (domain logic)
             * 
             * Infrastructure Layer
             * Database
             */

            /*1 - CommandInput*/
            var createPessoaCommandInput = new CreatePessoaCommandInput(createPessoaRequest.Nome, createPessoaRequest.Idade);
            /*2 - mediator sera responsavel por enviar o commandInput e sera responsavel por delegar a execução do command*/
            var createPessoaCommandResult = await _mediator.Send(createPessoaCommandInput);
            /*3 - é retornado um metodo herdado da classe BaseController*/
            return HandleMediatorResult(createPessoaCommandResult);
        }

    }
}
```

# Arquivo CommandInput dentro da pasta CreatePessoa dentro de commands na camada de Application:

```c#
using CRM.Kernel.Application;
using Microsoft.AspNetCore.Http;

namespace CRM.Suporte.Application.Commands.CreatePessoa
{
    //Dica para gerar um constructor: Botão direito na class, Quick Actions e generate constructor
	public class CreatePessoaCommandInput : CommandInput<CreatePessoaCommandResult>
    {
        public CreatePessoaCommandInput(string nome, int idade)
        {
            Nome = nome;
            Idade = idade;
        }

        public string Nome { get; }
        public int Idade { get; }

    }
}

```

# Desenvolvendo o CommandHandler e CommandResult

- Na camada de domain é onde deve estar toda a regra de negocio da aplicação, baseado na regra de domain driven design do projeto

- Na camada de domain crie uma pasta do dominio a ser criado, "PessoaAggregate" e dentro da pasta uma classe que sera
a raiz de agregação pessoa, "Pessoa.cs"

- A ideia é que dentro da classe Pessoa fique toda regra de negócio referente a lógica de dominio

# Arquivo Pessoa.cs dentro de PessoaAggregate na camada de domínio:

```c#
using CRM.Kernel.Domain;

namespace CRM.Suporte.Domain.PessoaAggregate
{
    //AuditableEntity é uma classe base para as entidades de um dominio que contem informaçães para auditoria da entidade 
    public class Pessoa : AuditableEntity, IAggregateRoot
    {
        public Pessoa(string nome, int idade)
        {
            Nome = nome;
            Idade = idade;
        }

        public string Nome { get; }
         public int Idade { get; }
    }
}
```

- Na camada de Infra é onde se lida com o banco de dados
  
- A ideia dessa camada é unificar qualquer logica de negocio possivel referente a commitar mudanças de estado de um dado atravez de uma classe

- Dentro da pasta Repositories, criar uma nova classe PessoaRepository.cs

# Arquivo PessoaRepository dentro da pasta Repositories na camada de Infra

```c#
using CRM.Kernel.Infra;
using CRM.Suporte.Domain.PessoaAggregate;
using System.Linq.Expressions;

namespace CRM.Suporte.Infra.Repositories
{
    public class PessoaRepository : GenericRepository<Pessoa>, IPessoaRepository
    {
        public PessoaRepository(EFCoreContext context) : base(context) =>  Expression.Empty();
        
    }
}
```

- No Arquivo context na camada de Infra é necessário criar um DbSet de Pessoa seguindo o padrão de DbSet ja criados:
```c#
using CRM.Suporte.Domain.PessoaAggregate;

public DbSet<Pessoa> Pessoas { get; set; }

modelBuilder.ApplyConfiguration(new PessoaMap());
```

- Desenvolver o mapeamento de "Pessoa" na pasta Maps da camada de Infra, Criar uma pasta "PessoaAggregate" dentro da pasta Maps e adicionar uma
Classe PessoaMap

- Essa classe PessoaMap serve para mapear  a entidade no banco de dados usando o entity framework core:

# Arquivo PessoaMap dentro da pasta Maps na camada de Infra

```c#
using CRM.Kernel.Infra;
using CRM.Suporte.Domain.PessoaAggregate;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace CRM.Suporte.Infra.Maps.PessoaAggregate
{
   public class PessoaMap : AuditableEntityMap<Pessoa>
    {
        public override void Configure(EntityTypeBuilder<Pessoa> builder)
        {
            //Mapeamento os dois campos que a classe pessoa Possui

            //Mapeando o field de Nome
            builder
                .Property(pessoa => pessoa.Nome)
                .HasColumnType("varchar(100)")
                .IsRequired();

            //Mapeando o field de Idade
            builder
                .Property(pessoa => pessoa.Idade)
                .IsRequired();

            base.Configure(builder);
        }
    }
}
```

- Seguindo o princípio do SOLID inversão de dependência, "Duas entidades do sistema não devem ser acopladas entre si"
 - Na pasta de PessoaAggregate na camada de domínio - new interface - IPessoaRepository:

- Esta implementação serve para que em qualquer ponto do código que for preciso acessar o repositório de pessoa, o repositório
Não seja acessado diretamente, será chamado a interface e será acessado os métodos do GenericRepository através dessa interface

```c#
using CRM.Kernel.Domain;

namespace CRM.Suporte.Domain.PessoaAggregate
{
    public interface IPessoaRepository : IGenericRepository<Pessoa>
    {
    }
}
```

- Enfim no arquivo CreatePessoaCommandHandler.cs é instanciado a entidade Pessoa para guardar no banco de dados

# Arquivo CreatePessoaCommandHandler.cs:

```c#
using CRM.Kernel.Application;
using CRM.Suporte.Domain.PessoaAggregate;
using System;
using System.Threading;
using System.Threading.Tasks;

namespace CRM.Suporte.Application.Commands.CreatePessoa

{
	public class CreatePessoaCommandHandler : ICommandHandler<CreatePessoaCommandInput, CreatePessoaCommandResult>
	{
		private readonly IPessoaRepository _pessoaRepository;

        public CreatePessoaCommandHandler(IPessoaRepository pessoaRepository)
        {
			//Verificando se o pessoaRepository vem como null
            _pessoaRepository = pessoaRepository ?? throw new ArgumentNullException(nameof(pessoaRepository));
        }

        public async Task<CreatePessoaCommandResult> Handle(CreatePessoaCommandInput command, CancellationToken cancellationToken)
		{
			/*
			 * 1 Criar uma nova instancia da entidade
			 * 2 Adicionar ao repository
			 * 3 Commitar as alteracoes no repository
			 * 4 Retornar o commandResult
			 */


			//1 Instancia da entidade Pessoa para guardar no banco de dados
			var pessoa = new Pessoa(
				nome: command.Nome,
				idade: command.Idade
				);

			//2 Chamar o repository
			await _pessoaRepository.AddAsync(pessoa, cancellationToken);

			//3 Commitar as alteracoes
			await _pessoaRepository.UnitOfWork.CommitAsync(cancellationToken);

			//4 Retornar um commandResult passando o id da pessoa criado como parametro
			//Esse id vem de dentro de AuditableEntity
			return new CreatePessoaCommandResult(pessoa.Id);
		}

	}
}
```

# Arquivo CommandResult:

```c#
using CRM.Kernel.Application;
using System;

namespace CRM.Suporte.Application.Commands.CreatePessoa
{
    public class CreatePessoaCommandResult : CommandResult
    {
        public CreatePessoaCommandResult(Guid pessoaId)
        {
            PessoaId = pessoaId;
        }

        //Guid é uma implementação da microsoft de um UUID
        public Guid PessoaId { get; }
    }
}
```

- No arquivo Startup.cs na camada de API deve ser implementado uma classe da interface IPessoaRepository

- Para quando o framework tentar criar uma instancia de CreatePessoaCommandHandler ele sabera que essa classe possui essa dependencia

```c#
services.AddScoped<IPessoaRepository, PessoaRepository>();
```

- No visualStudio vá em tools -> NuGet Package Manager -> Package Manager Console, no console setar em default project o projeto de infra e digitar no console add-migration AddPessoa para rodar a migration e criar as tabelas no banco de dados

- A aplicação está pronta para testar após isso!


# Desenvolvendo um endpoint para resgatar esses dados

- No arquivo MinhaController escrever um novo método
  
```c#
  [HttpGet("pessoa/{pessoaId:guid}")]
        public async Task<IActionResult> GetPessoa(Guid pessoaId, CancellationToken cancellationToken)
        {
            var getPessoaQueryInput = new GetPessoaQueryInput(pessoaId);
            var getPessoaQueryResult = await _mediator.Send(getPessoaQueryInput);

            return HandleMediatorResult(getPessoaQueryResult);
        }
```

- Criar uma nova pasta GetPessoa dentro de Querys na camada de Application, adicionar 3 classes - Input, Handle, Result

# Arquivo GetPessoaQueryHandler dentro de GetPessoa:

```c#
using CRM.Kernel.Application;
using CRM.Suporte.Domain.PessoaAggregate;
using Microsoft.EntityFrameworkCore;
using System;
using System.Net;
using System.Threading;
using System.Threading.Tasks;

namespace CRM.Suporte.Application.Queries.GetPessoa
{
    public class GetPessoaQueryHandler : IQueryHandler<GetPessoaQueryInput, GetPessoaQueryResult>
    {
        /*
         * 1 Repository
         */

        private readonly IPessoaRepository _pessoaRepository;

        public GetPessoaQueryHandler(IPessoaRepository pessoaRepository)
        {
            _pessoaRepository = pessoaRepository ?? throw new ArgumentNullException(nameof(pessoaRepository));
        }
        public async Task<GetPessoaQueryResult> Handle(GetPessoaQueryInput query, CancellationToken cancellationToken)
        {
            /*
             * 1 Chamar o repository.GetAsNoTracking do repositorio para obeter uma referencia da entidade do repositorio
             * 2 return query result
             */

            var existingPessoa = await _pessoaRepository
                .GetAsNoTracking(pessoa => pessoa.Id == query.PessoaId)
                .SingleOrDefaultAsync(cancellationToken);

            if(existingPessoa is null)
            {
                return new GetPessoaQueryResult().WithHttpStatusCode(HttpStatusCode.NotFound) as GetPessoaQueryResult;
            }

            return new GetPessoaQueryResult
            {
                Nome = existingPessoa.Nome,
                Idade = existingPessoa.Idade
            };
        }
    }
}
```

# Arquivo GetPessoaQueryInput dentro de GetPessoa

```c#
using CRM.Kernel.Application;
using System;

namespace CRM.Suporte.Application.Queries.GetPessoa
{
    public class GetPessoaQueryInput : QueryInput<GetPessoaQueryResult>
    {
        public Guid PessoaId { get; }

        public GetPessoaQueryInput(Guid id)
        {
            PessoaId = id;
        }
    }
}
```

# Arquivo GetPessoaQueryResult dentro de GetPessoa

```c#
using CRM.Kernel.Application;
using CRM.Suporte.Domain.ImportedFileAggregate;

namespace CRM.Suporte.Application.Queries.GetPessoa
{
    public class GetPessoaQueryResult : QueryResult
    {

        public string Nome { get; init; }
        public int Idade { get; init; }
    }
}
```
- token: Bearer eyJ4NXQiOiJNell4TW1Ga09HWXdNV0kwWldObU5EY3hOR1l3WW1NNFpUQTNNV0kyTkRBelpHUXpOR00wWkdSbE5qSmtPREZrWkRSaU9URmtNV0ZoTXpVMlpHVmxOZyIsImtpZCI6Ik16WXhNbUZrT0dZd01XSTBaV05tTkRjeE5HWXdZbU00WlRBM01XSTJOREF6WkdRek5HTTBaR1JsTmpKa09ERmtaRFJpT1RGa01XRmhNelUyWkdWbE5nX1JTMjU2IiwiYWxnIjoiUlMyNTYifQ.eyJhdF9oYXNoIjoiSkQxaFl2SVNoMnBqd0xDeGdKT2FOdyIsImF1ZCI6IjRER1BhRmpEbFZ2VVYyYVplWjZqTHIwYVhuc2EiLCJzdWIiOiJvcGVyYWRvcjIiLCJ1cG4iOiIwYmZjYjlhOS05Mzc4LTRlZWEtOTk4YS0xODI0MTM5YWEwODIiLCJuYmYiOjE2MzE1NTM1NjksImF6cCI6IjRER1BhRmpEbFZ2VVYyYVplWjZqTHIwYVhuc2EiLCJhbXIiOlsicGFzc3dvcmQiXSwiaXNzIjoiaHR0cHM6XC9cL3dzby5kZXYucGFzY2hvYWxvdHRvLmNvbS5icjo0NDNcL29hdXRoMlwvdG9rZW4iLCJuYW1lIjoib3BlcmFkb3IyIFRFU1RFIiwiZXhwIjoxNjMxNjM5OTY5LCJpYXQiOjE2MzE1NTM1Njl9.DML6coGoo8XzHuEo2wVNFajuzKTr-EFGjUbj4pMybcP7C0D-wRkhtbIBzmS09uIMJdmI0JJ2M99Aq3rn-wRtD8rhq35ny_n3irBHhctdL0PfbPsY7HlMMw3FzHO1rbolI-sF3vPhmKA3lq_loJ9UUuchQukyIAz3Yy9vw4adqEEYnvSfLAB3cnUlOxf5jBRtmZsMRF4nJTGu_Yypjh3eGRFkgMoDnJXfPko17UdryfOLpu4lR-ZBBql1w2CctoBu46o4wEzAuZifa-2g3lzLT4k5hfuUpEgQFs-L6ax3zt_3PVzvKHuqvYCMZ_UXvKg9DxHb4s1vbds0BvUxUiVpyw