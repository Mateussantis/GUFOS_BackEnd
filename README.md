# GUFOS - Agenda de Eventos - BackEnd C# - .Net Core 3.0

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

> Criamos nosso primeiro Controller: CategoriaController
> Herdamos nosso novo controller de ControllerBase
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

> Criamos nosso método GET:
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

> Criamos nossa sobrecarga de método GET, desta vez passando como parâmetro o ID:
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

> Criamos nosso método PUT para atualizar os dados:
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





