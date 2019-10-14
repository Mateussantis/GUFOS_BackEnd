# GUFOS - Agenda de Eventos - BackEnd C# - .Net Core 3.0

## Requisitos
> - Visual Studio Code <br>
> - Banco de Dados funcionando - DDLs, DMLs e DQLs <br>
> - .NET Core SDK 3.0

## Criação do Projeto
> Criamos nosso projeto de API com: 
```bash
dotnet new webapi
```
<br>

## Entity Framework - Database First

> Instalar o gerenciador de pacotes EF na máquina:
```bash
dotnet tool install --global dotnet-ef
```

<br>

> Baixar Pacote SQL Server:
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<br>

> Baixar pacote de escrita de códigos do EF:
```bash
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<br>

> Dar um restore na aplicação para ler e aplicar os pacotes instalados:
```bash
dotnet restore
```

<br>

> Testar se o EF está ok
```bash
dotnet ef
```

<br>

> Criar os Models à partir da sua base de Dados
    :point_right: -o = criar o diretorio caso não exista
    :point_right: -d = Incluir as DataNotations do banco
```bash
dotnet ef dbcontext scaffold "Server=DESKTOP-XVGT587\SQLEXPRESS;Database=Gufos;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -d
```
<br>

## Controllers
> Apagamos o controller que já vem com a base...

### CategoriaController

> Criamos nosso primeiro Controller: CategoriaController <br>
> Herdamos nosso novo controller de ControllerBase <br>
> Definimos a "rota" da API logo em cima do nome da classe, utilizando:
```c#
[Route("api/[controller]")]
```
> Logo abaixo dizemos que é um controller de API, utilizando:
```c#
[ApiController]
```
<br>

> Damos **CTRL + .** para incluir:

```c#
using Microsoft.AspNetCore.Mvc;
```
<br>

> Instanciamos nosso contexto da nossa Base de Dados:
```c#
GufosContext _contexto = new GufosContext();
```

> Damos **CTRL + .** para incluir nossos models:
```c#
using GUFOS_BackEnd.Models;
```

> Criamos nosso método **GET**:
```c#
        // GET: api/Categoria/
        [HttpGet]
        public async Task<ActionResult<List<Categoria>>> Get()
        {
            var categorias = await _context.Categoria.ToListAsync();

            if (categorias == null)
            {
                return NotFound();
            }

            return categorias;
        }
```

> Importamos com CTRL + . as dependências:
```c#
using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;
using System.Collections.Generic;
```
<br>

> Testamos o método GET de nosso controller no Postman:
```bash
dotnet run
https://localhost:5001/api/categoria
```

> Deve ser retornado:
```json
[
    {
        "categoriaId": 1,
        "titulo": "Desenvolvimento",
        "evento": []
    },
    {
        "categoriaId": 2,
        "titulo": "HTML + CSS",
        "evento": []
    },
    {
        "categoriaId": 3,
        "titulo": "Marketing",
        "evento": []
    }
]
```

<br>

> Criamos nossa sobrecarga de método **GET**, desta vez passando como parâmetro o ID:
```c#
        // GET: api/Categoria/5
        [HttpGet("{id}")]
        public async Task<ActionResult<Categoria>> Get(int id)
        {
            var categoria = await _context.Categoria.FindAsync(id);

            if (categoria == null)
            {
                return NotFound();
            }

            return categoria;
        }
```
> Testamos no Postman: [https://localhost:5001/api/categoria/1](https://localhost:5001/api/categoria/1)

<br>

> Criamos nosso método **POST** para inserir uma nova categoria:
```c#
        // POST: api/Categoria/
        [HttpPost]
        public async Task<ActionResult<Categoria>> Post(Categoria categoria)
        {
            try
            {
                await _context.AddAsync(categoria);
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                throw;
            }

            return categoria;
        }
```
> Testamos no Postman, passando em RAW , do tipo JSON:
```json
{
    "titulo": "Teste"
}
```

<br>

> Criamos nosso método **PUT** para atualizar os dados:
```c#
        // PUT: api/Categoria/5
        [HttpPut("{id}")]
        public async Task<IActionResult> Put(long id, Categoria categoria)
        {
            if (id != categoria.CategoriaId)
            {
                return BadRequest();
            }

            _context.Entry(categoria).State = EntityState.Modified;

            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                var categoria_valido = await _context.Categoria.FindAsync(id);

                if (categoria_valido == null)
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return NoContent();
        }
```
> Testamos no Postman, no método PUT, pela URL [https://localhost:5001/api/categoria/4](https://localhost:5001/api/categoria/4) passando:
```json
{
    "categoriaId": 4,
    "titulo": "Design Gráfico"
}
```

<br>

> Por último, incluímos nosso método **DELETE** , para excluir uma determinada Categoria:
```c#
        // DELETE: api/Categoria/5
        [HttpDelete("{id}")]
        public async Task<ActionResult<Categoria>> Delete(int id)
        {
            var categoria = await _context.Categoria.FindAsync(id);
            if (categoria == null)
            {
                return NotFound();
            }

            _context.Categoria.Remove(categoria);
            await _context.SaveChangesAsync();

            return categoria;
        }
```
> Testamos pelo Postman, pelo mérodo DELETE, e com a URL: [https://localhost:5001/api/categoria/4](https://localhost:5001/api/categoria/4) <br>
> Deve-se retornar o objeto excluído:
```json
{
    "categoriaId": 4,
    "titulo": "Design Gráfico",
    "evento": []
}
```

<br>

### LocalizacaoController
> Copiar ControllerCategoria e alterar com **CTRL + F** <br>
> Testar os métodos REST

<br>

### EventoController
> Copiar ControllerCategoria e alterar com **CTRL + F** <br>
> Testar os métodos REST <br>
> Notamos que no método **GET** não retorna a árvore de objetos *Categoria* e *Localizacao* <br>
> Para incluirmos é necessário adicionar em nosso projeto o seguinte pacote:
```bash
dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
```
> Depois em nossa Startup.cs, dentro de ConfigureServices, no lugar de *services.AddControllers()* :
```c#
services.AddControllersWithViews().AddNewtonsoftJson(opt => opt.SerializerSettings.ReferenceLoopHandling = ReferenceLoopHandling.Ignore);
```
> Damos **CTRL + .** para incluir a dependência:
```c#
using Newtonsoft.Json;
```
> Após isso precisamos mudar nosso controller para receber os atributos, no método GET ficará assim:
```c#
var eventos = await _context.Evento.Include(c => c.Categoria).Include(l => l.Localizacao).ToListAsync();
```
> No método GET com parâmetro ficará assim:
```c#
var evento = await _context.Evento.Include(c => c.Categoria).Include(l => l.Localizacao).FirstOrDefaultAsync(e => e.EventoId == id);
```

<br>

> Adicionar os Controllers restantes

<br><br>

## SWAGGER - Documentação da API

>  Instalar o Swagger:
```bash
dotnet add Gufos_BackEnd.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc4
```
<br>

> Registramos o gerador do Swagger dentro de ConfigureServices, definindo 1 ou mais documentos do Swagger:
```c#
            services.AddSwaggerGen(c =>
            {
                c.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1" });
                // Mostrar o caminho dos comentários dos métodos Swagger JSON and UI.
                var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
                var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
                c.IncludeXmlComments(xmlPath);
            });
```

<br>

> Colocar na Startup com **CTRL + .**
```c#
using Microsoft.OpenApi.Models;
using System.Reflection;
using System.IO;
```

<br>

> Colocar dentro do csproj para gerar a documentação com base nos comentários dos métodos:
```c#
  <PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
  </PropertyGroup>
```

<br>

> Em Startup.cs , no método Configure, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:
```c#
            // Habilitamos efetivamente o Swagger em nossa aplicação.
            app.UseSwagger();
            // Especificamos o endpoint da documentação
            app.UseSwaggerUI(c =>
            {
                c.SwaggerEndpoint("/swagger/v1/swagger.json", "API V1");
            });
```

<br>

> Rodar a aplicação e testar em: [https://localhost:5001/swagger/](https://localhost:5001/swagger/)






