# 🏆 RocketSeat NLW Expert - Auction API

Uma aplicação profissional de **API RESTful** para gerenciamento de leilões, desenvolvida durante o evento **NLW Expert** da RocketSeat, utilizando as melhores práticas de **C#** e **.NET 8.0**.

## 📋 Sobre o Projeto

RocketSeatAuction é uma API que permite:
- 🎯 Criar e gerenciar leilões
- 💰 Registrar e processar lances
- 👥 Gerenciar usuários
- 📊 Acompanhar leilões em tempo real
- 🔐 Autenticação de usuários

## 🛠️ Tecnologias

- **Linguagem**: C# 12
- **Framework**: ASP.NET Core 8.0
- **Banco de Dados**: SQL Server
- **Arquitetura**: Clean Architecture com padrão Repository
- **Documentação API**: Swagger/OpenAPI
- **Container**: Docker (opcional)

## 📁 Estrutura do Projeto

```
RocketSeatNLWExpert/
├── Fundamentos/
│   └── Documentacao_CSharp_DotNet.md          # Guia de fundamentos
├── Conceitos/
│   └── Documentacao_CSharp_DotNet_Avancado.md # Conceitos avançados
├── Docs/
│   ├── Conceitos/
│   └── Fundamentos/
├── RocketSeatAuction.API/
│   ├── Controllers/          # Endpoints da API
│   ├── Entities/            # Modelos de domínio
│   ├── Repositories/        # Acesso a dados
│   ├── Services/            # Lógica de negócio
│   ├── UseCases/            # Casos de uso
│   ├── Communication/       # DTOs
│   ├── Filters/             # Middlewares customizados
│   ├── Enums/               # Enumerações
│   ├── appsettings.json     # Configurações
│   └── Program.cs           # Ponto de entrada
├── RocketSeatLeilao.sln     # Solução Visual Studio
└── README.md                # Este arquivo
```

## 🚀 Como Executar

### Pré-requisitos
- .NET 8.0 SDK ou superior
- Visual Studio 2022 / VS Code
- SQL Server (local ou container)

### Passos

1. **Clone o repositório**
```bash
git clone https://github.com/gabrielsalesdavid/RocketSeatNLWExpert.git
cd RocketSeatNLWExpert
```

2. **Restaure as dependências**
```bash
cd RocketSeatAuction.API
dotnet restore
```

3. **Configure o banco de dados**
Edite `appsettings.Development.json` com sua string de conexão:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=seu-servidor;Database=RocketSeatAuction;Trusted_Connection=true;"
  }
}
```

4. **Execute a aplicação**
```bash
dotnet run
```

A API estará disponível em `https://localhost:5001`

## 📖 Documentação

### Fundamentos de C# e .NET 8.0
Aprenda os conceitos básicos necessários para trabalhar com C#:
- [📚 Documentação de Fundamentos](./Fundamentos/Documentacao_CSharp_DotNet.md)

Tópicos cobertos:
- Variáveis e tipos de dados
- Operadores e expressões
- Estruturas de controle
- Programação orientada a objetos
- Coleções e LINQ
- Namespaces e tratamento de exceções

### Conceitos Avançados
Explore padrões profissionais e arquiteturas escaláveis:
- [🎓 Documentação de Conceitos Avançados](./Conceitos/Documentacao_CSharp_DotNet_Avancado.md)

Tópicos cobertos:
- **SOLID Principles** - Código limpo e manutenível
- **Padrões de Design** - Singleton, Factory, Strategy, Observer
- **Dependency Injection** - Injeção de dependências
- **Async/Await** - Programação assíncrona
- **Generics** - Type safety
- **LINQ** - Consultas elegantes
- **Entity Framework Core** - Persistência de dados

## 🏗️ Arquitetura

O projeto segue os princípios de **Clean Architecture**:

```
┌─────────────────────────────────┐
│   Presentation (Controllers)    │
├─────────────────────────────────┤
│   Application (Services/UseCases)
├─────────────────────────────────┤
│   Domain (Entities)             │
├─────────────────────────────────┤
│   Infrastructure (Repositories) │
└─────────────────────────────────┘
```

**Benefícios:**
- ✅ Independência de frameworks
- ✅ Testabilidade
- ✅ Manutenibilidade
- ✅ Escalabilidade

## 🔌 Endpoints Principais

### Leilões
```
GET    /api/auctions                  # Listar leilões
GET    /api/auctions/{id}             # Obter leilão específico
POST   /api/auctions                  # Criar leilão
PUT    /api/auctions/{id}             # Atualizar leilão
DELETE /api/auctions/{id}             # Deletar leilão
```

### Lances
```
POST   /api/offers                    # Criar lance
GET    /api/offers/{auctionId}        # Obter lances do leilão
```

## 🔐 Autenticação

A API utiliza autenticação baseada em atributos customizados:
- `[AuthenticationUser]` - Filtra requisições não autenticadas
- Validação de usuário logado

## 📝 Padrões e Boas Práticas

### Repository Pattern
```csharp
public interface IRepository<T>
{
    Task<T> ObterPorId(int id);
    Task<List<T>> ObterTodos();
    Task Adicionar(T entidade);
    Task Atualizar(T entidade);
    Task Remover(int id);
}
```

### Dependency Injection
```csharp
builder.Services.AddScoped<IRepositorio, Repositorio>();
builder.Services.AddTransient<IServico, Servico>();
builder.Services.AddSingleton<IConfiguracao, Configuracao>();
```

### DTOs (Data Transfer Objects)
```csharp
public class RequestCreateOfferJson
{
    public decimal Value { get; set; }
    public int AuctionId { get; set; }
}
```

## 🧪 Testes

Testes unitários e de integração devem ser adicionados em um projeto separado:

```bash
dotnet new xunit -n RocketSeatAuction.Tests
dotnet add RocketSeatAuction.Tests reference RocketSeatAuction.API
```

## 🐳 Docker (Opcional)

Para executar a aplicação em container:

```bash
docker build -t rocketseat-auction .
docker run -p 5001:8080 rocketseat-auction
```

## 🤝 Contribuindo

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📚 Recursos Adicionais

- [Documentação oficial do C#](https://learn.microsoft.com/pt-br/dotnet/csharp/)
- [ASP.NET Core Documentation](https://learn.microsoft.com/pt-br/aspnet/core/)
- [Entity Framework Core](https://learn.microsoft.com/pt-br/ef/core/)
- [Clean Architecture - Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)

## 📊 Swagger/OpenAPI

Acesse a documentação interativa da API em:
```
https://localhost:5001/swagger/index.html
```

## ✅ Checklist de Aprendizado

- [ ] Entender a arquitetura do projeto
- [ ] Dominar os fundamentos de C# e .NET
- [ ] Aprender SOLID Principles
- [ ] Implementar novos endpoints
- [ ] Escrever testes unitários
- [ ] Configurar banco de dados
- [ ] Explorar Entity Framework Core
- [ ] Implementar autenticação/autorização
- [ ] Fazer deploy em produção

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## 👨‍💻 Autor

**Gabriel Sales David**

- GitHub: [@gabrielsalesdavid](https://github.com/gabrielsalesdavid)
- LinkedIn: [gabriel-sales-david](https://linkedin.com/in/gabriel-sales-david)

## 🙏 Agradecimentos

- [RocketSeat](https://www.rocketseat.com.br/) - Educação de excelência em tecnologia
- NLW Expert - Comunidade incrível de desenvolvedores

---

**Última atualização**: 13 de janeiro de 2026

Desenvolvido com ❤️ durante o NLW Expert da RocketSeat
