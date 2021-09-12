# Desenvolvendo um endpoint

- Inicie o projeto abrindo uma solução - microserviço -  suporte

- Endpoints ficam dentro da pasta controller, basicamente uma classe dentro da camada de API que herda da classe BaseController

- Dentro da pasta controller
  - Add new item - Class - MinhaController

- Esta classe devera ser herdada da classe BaseController dentro do namespace Kernel-API

- Copie uma requisicao do tipo POST de algum controller ja pronto para o reaproveitamento de codigo e para manter o Design Pattern

- Na camada de API crie uma pasta Requests para o desenvolvimento de um DTO, um conceito de transferencia de dados entre camadas dentro do projeto

- Na pasta Requests adicione uma nova classe chamada - IncludePessoaRequest

- Este record sera necessario para criar uma classe que sera usada apenas para transferir dados de uma camada para outra

- No arquivo MinhaController adicione esse record como parametro do método que ira usa-lo para consumir esses dados

- Dentro do escopo da classe adicione - return Ok("Passei aqui");

- Execute o projeto CRM.Suporte.API junto com docker para verificar se se a solution ja esta funcionando

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

# Desenvolvendo um command

- Na pasta Commands - crie uma nova pasta chamada - CreatePessoa

- A ideia é que o command não tenha lógica de regra de negócio alguma, um command irá apenas orquestrar uma execução de modulos menores do sistema

- Padrão de implementação especifico das classes de um command - CommandHandler - CommandInput - CommandResult - CommandValidator

- Dica de implementação - Entrar em uma pasta de Commands, copiar os 4 aquivos padrão e colar na pasta do novo command e renomear os arquivos e as classes alterando somente
o necessário

- O commandHandler serve para disponibilizar um implementação que vai reagir a um commandInput

- O controller ira gerar um commandInput baseado no request que foi feito no endpoint com os dados da requisição

- Os dados enviados na request são recebidos na Request folder, no CreatePessoaRequest que esta no parametro da controller

- Esses dados são passados para a camada de application atraves de um commandInput

- No Constructor MinhaController sera criado uma variavel commandInput, esse commandInput sera enviado para o mediator, e sera retornado uma resposta

- Apos criar as 4 classes do design pattern do command e tipar corretamente as mesmas, o mediator ja sabe que deve chamar o commandHandler e também sabe que 
ele deve retornar um commanResult

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
            //  return Ok("Passei aqui");

            /*
             *1-  Criar um commandInput
             *2-  Enviar o commandInput pro mediator
             *3-  Retornar uma resposta do envio do command
             */

            // 1 - CommandInput
            var createPessoaCommandInput = new CreatePessoaCommandInput(createPessoaRequest.Nome, createPessoaRequest.Idade);
            // 2 - mediator sera responsavel por enviar o commandInput e sera responsavel por delegar a execução do command
            var createPessoaCommandResult = await _mediator.Send(createPessoaCommandInput);
            //3 - é retornado um metodo herdado da classe BaseController
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

# Implementando o CommandHandler

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
    public class PessoaRepository : GenericRepository<Pessoa>
    {
        public PessoaRepository(EFCoreContext context) : base(context) =>  Expression.Empty();
        
    }
}
```

- No Arquivo context na camada de Infra é necessário criar um DbSet de Pessoa seguindo o padrão de DbSet ja criados:
```c#
using CRM.Suporte.Domain.PessoaAggregate;

public DbSet<Pessoa> Pessoas { get; set; }
```