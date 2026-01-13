# Documentação de Conceitos Avançados - C# e .NET 8.0

## 📋 Índice
1. [SOLID Principles](#solid-principles)
2. [Padrões de Design](#padrões-de-design)
3. [Dependency Injection](#dependency-injection)
4. [Async/Await](#asyncawait)
5. [Generics](#generics)
6. [Reflection](#reflection)
7. [Delegates e Events](#delegates-e-events)
8. [LINQ](#linq)
9. [Entity Framework Core](#entity-framework-core)

---

## SOLID Principles

SOLID é um acrônimo para cinco princípios de design que tornam o software mais compreensível, flexível e fácil de manter.

### Single Responsibility Principle (SRP)

Uma classe deve ter uma única responsabilidade, ou seja, um único motivo para mudar.

❌ **Mal**
```csharp
public class Usuario
{
    public string Nome { get; set; }
    
    // Responsabilidade 1: Dados do usuário
    public void ValidarEmail(string email) { }
    
    // Responsabilidade 2: Persistência
    public void SalvarNoBanco() { }
    
    // Responsabilidade 3: Envio de email
    public void EnviarEmail(string assunto, string corpo) { }
}
```

✅ **Bem**
```csharp
public class Usuario
{
    public string Nome { get; set; }
    public string Email { get; set; }
}

public class ValidadorUsuario
{
    public bool ValidarEmail(string email) => email.Contains("@");
}

public class RepositorioUsuario
{
    public void Salvar(Usuario usuario) { }
}

public class ServicEmailUsuario
{
    public void EnviarEmail(Usuario usuario, string assunto) { }
}
```

### Open/Closed Principle (OCP)

Aberto para extensão, mas fechado para modificação.

❌ **Mal**
```csharp
public class CalculadoraDesconto
{
    public decimal Calcular(Usuario usuario, decimal valor)
    {
        if (usuario.Tipo == "Premium")
            return valor * 0.9m;
        else if (usuario.Tipo == "Gold")
            return valor * 0.8m;
        else
            return valor;
    }
}
// Toda nova categoria exige modificação da classe
```

✅ **Bem**
```csharp
public interface ICalculadoraDesconto
{
    decimal Calcular(decimal valor);
}

public class DescontoGold : ICalculadoraDesconto
{
    public decimal Calcular(decimal valor) => valor * 0.8m;
}

public class DescontoPremium : ICalculadoraDesconto
{
    public decimal Calcular(decimal valor) => valor * 0.9m;
}

public class DescontoNenhum : ICalculadoraDesconto
{
    public decimal Calcular(decimal valor) => valor;
}

public class Usuario
{
    public ICalculadoraDesconto Desconto { get; set; }
}
```

### Liskov Substitution Principle (LSP)

Objetos de uma classe derivada devem poder substituir objetos da classe base sem quebrar a aplicação.

❌ **Mal**
```csharp
public class Ave
{
    public virtual void Voar() { }
}

public class Passaro : Ave
{
    public override void Voar() { Console.WriteLine("Voando"); }
}

public class Pinguim : Ave
{
    public override void Voar() 
    { 
        throw new InvalidOperationException("Pinguim não voa");
    }
}

public class Aviador
{
    public void FazerVoar(Ave ave)
    {
        ave.Voar();  // Pinguim quebraria aqui!
    }
}
```

✅ **Bem**
```csharp
public class Animal { }

public interface IVoador
{
    void Voar();
}

public class Passaro : Animal, IVoador
{
    public void Voar() { Console.WriteLine("Voando"); }
}

public class Pinguim : Animal { }

public class Aviador
{
    public void FazerVoar(IVoador voador)
    {
        voador.Voar();
    }
}
```

### Interface Segregation Principle (ISP)

Crie interfaces específicas em vez de genéricas. Clientes não devem ser forçados a depender de interfaces que não usam.

❌ **Mal**
```csharp
public interface ITrabalhador
{
    void Trabalhar();
    void Comer();
}

public class Robo : ITrabalhador
{
    public void Trabalhar() { }
    
    public void Comer() 
    { 
        throw new NotImplementedException("Robô não come");
    }
}
```

✅ **Bem**
```csharp
public interface ITrabalhador
{
    void Trabalhar();
}

public interface IComivel
{
    void Comer();
}

public class Pessoa : ITrabalhador, IComivel
{
    public void Trabalhar() { }
    public void Comer() { }
}

public class Robo : ITrabalhador
{
    public void Trabalhar() { }
}
```

### Dependency Inversion Principle (DIP)

Dependa de abstrações, não de implementações concretas.

❌ **Mal**
```csharp
public class RepositorioSQL
{
    public void Salvar(string dados) { }
}

public class Servico
{
    private RepositorioSQL _repo = new();  // Dependência direta
    
    public void Processar(string dados)
    {
        _repo.Salvar(dados);
    }
}
```

✅ **Bem**
```csharp
public interface IRepositorio
{
    void Salvar(string dados);
}

public class RepositorioSQL : IRepositorio
{
    public void Salvar(string dados) { }
}

public class Servico
{
    private IRepositorio _repo;
    
    public Servico(IRepositorio repo)
    {
        _repo = repo;  // Injetar dependência
    }
    
    public void Processar(string dados)
    {
        _repo.Salvar(dados);
    }
}
```

---

## Padrões de Design

### Singleton

Garante que uma classe tenha apenas uma instância e fornece um ponto de acesso global a ela.

```csharp
public sealed class Configuracao
{
    private static Configuracao _instancia;
    private static readonly object _lock = new();
    
    public string ChaveApi { get; set; }
    
    private Configuracao() { }
    
    public static Configuracao Obter()
    {
        if (_instancia == null)
        {
            lock (_lock)
            {
                if (_instancia == null)
                    _instancia = new Configuracao();
            }
        }
        return _instancia;
    }
}

// Uso
var config = Configuracao.Obter();
config.ChaveApi = "minha-chave";

var config2 = Configuracao.Obter();
// config == config2, mesma instância
```

**Lazy<T> (mais seguro)**
```csharp
public sealed class Configuracao
{
    private static readonly Lazy<Configuracao> _instancia = 
        new(() => new Configuracao());
    
    public string ChaveApi { get; set; }
    
    private Configuracao() { }
    
    public static Configuracao Obter() => _instancia.Value;
}
```

### Factory Pattern

Cria objetos sem especificar as classes concretas exatas.

```csharp
public abstract class Veiculo
{
    public abstract void Acelerar();
}

public class Carro : Veiculo
{
    public override void Acelerar() => Console.WriteLine("Carro acelerou");
}

public class Moto : Veiculo
{
    public override void Acelerar() => Console.WriteLine("Moto acelerou");
}

public interface IFabricaVeiculo
{
    Veiculo CriarVeiculo();
}

public class FabricaCarro : IFabricaVeiculo
{
    public Veiculo CriarVeiculo() => new Carro();
}

public class FabricaMoto : IFabricaVeiculo
{
    public Veiculo CriarVeiculo() => new Moto();
}

// Factory Method
public static class FabricaVeiculo
{
    public static Veiculo Criar(string tipo) => tipo.ToLower() switch
    {
        "carro" => new Carro(),
        "moto" => new Moto(),
        _ => throw new ArgumentException("Tipo inválido")
    };
}

// Uso
var veiculo = FabricaVeiculo.Criar("carro");
veiculo.Acelerar();
```

### Strategy Pattern

Define uma família de algoritmos, encapsula cada um e os torna intercambiáveis.

```csharp
public interface ICalculadoraFrete
{
    decimal Calcular(decimal peso);
}

public class FreteStandard : ICalculadoraFrete
{
    public decimal Calcular(decimal peso) => peso * 10;
}

public class FreteExpresso : ICalculadoraFrete
{
    public decimal Calcular(decimal peso) => peso * 25;
}

public class FreteEconomico : ICalculadoraFrete
{
    public decimal Calcular(decimal peso) => peso * 5;
}

public class Pedido
{
    private ICalculadoraFrete _calculadora;
    
    public Pedido(ICalculadoraFrete calculadora)
    {
        _calculadora = calculadora;
    }
    
    public void AlterarEstrategia(ICalculadoraFrete novaCalculadora)
    {
        _calculadora = novaCalculadora;
    }
    
    public decimal CalcularFrete(decimal peso) => _calculadora.Calcular(peso);
}

// Uso
var pedido = new Pedido(new FreteStandard());
Console.WriteLine(pedido.CalcularFrete(10m));  // 100

pedido.AlterarEstrategia(new FreteExpresso());
Console.WriteLine(pedido.CalcularFrete(10m));  // 250
```

### Observer Pattern

Define uma dependência um-para-muitos entre objetos de forma que quando um objeto muda de estado, todos os seus dependentes são notificados automaticamente.

```csharp
public class Leilao
{
    private List<IObservadorLeilao> _observadores = new();
    
    public event EventHandler<LeilaoAlteradoEventArgs> Alterado;
    
    private decimal _maiorLance = 0;
    
    public void Registrar(IObservadorLeilao observador)
    {
        _observadores.Add(observador);
    }
    
    public void Receber(Lance lance)
    {
        if (lance.Valor > _maiorLance)
        {
            _maiorLance = lance.Valor;
            
            // Notificar todos os observadores
            foreach (var obs in _observadores)
            {
                obs.Atualizar(lance);
            }
            
            Alterado?.Invoke(this, new(lance));
        }
    }
}

public interface IObservadorLeilao
{
    void Atualizar(Lance lance);
}

public class LogObservador : IObservadorLeilao
{
    public void Atualizar(Lance lance)
    {
        Console.WriteLine($"[LOG] Novo lance: {lance.Valor}");
    }
}

public class EmailObservador : IObservadorLeilao
{
    public void Atualizar(Lance lance)
    {
        Console.WriteLine($"[EMAIL] Enviando notificação de novo lance");
    }
}

public class Lance
{
    public decimal Valor { get; set; }
}

public class LeilaoAlteradoEventArgs : EventArgs
{
    public Lance Lance { get; }
    public LeilaoAlteradoEventArgs(Lance lance) => Lance = lance;
}

// Uso
var leilao = new Leilao();
leilao.Registrar(new LogObservador());
leilao.Registrar(new EmailObservador());

leilao.Receber(new Lance { Valor = 100 });
// Output:
// [LOG] Novo lance: 100
// [EMAIL] Enviando notificação de novo lance
```

---

## Dependency Injection

Dependency Injection (DI) é o ato de fornecer dependências para um objeto.

### Tipos de DI

#### Constructor Injection
```csharp
public interface IRepositorio
{
    void Salvar(string dados);
}

public class Servico
{
    private readonly IRepositorio _repositorio;
    
    public Servico(IRepositorio repositorio)
    {
        _repositorio = repositorio ?? throw new ArgumentNullException(nameof(repositorio));
    }
    
    public void Processar(string dados)
    {
        _repositorio.Salvar(dados);
    }
}
```

#### Property Injection
```csharp
public class Servico
{
    public IRepositorio Repositorio { get; set; }
    
    public void Processar(string dados)
    {
        Repositorio?.Salvar(dados);
    }
}
```

#### Method Injection
```csharp
public class Servico
{
    public void Processar(string dados, IRepositorio repositorio)
    {
        repositorio.Salvar(dados);
    }
}
```

### DI Container (ASP.NET Core)

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Registrar serviços
builder.Services.AddScoped<IRepositorio, Repositorio>();
builder.Services.AddTransient<IServico, Servico>();
builder.Services.AddSingleton<IConfiguracao, Configuracao>();

var app = builder.Build();
app.Run();
```

**Lifetimes:**
- **Transient**: Nova instância a cada requisição
- **Scoped**: Uma instância por requisição/escopo
- **Singleton**: Uma instância para toda aplicação

---

## Async/Await

Programação assíncrona permite que operações I/O-bound não bloqueiem a thread.

### Básico
```csharp
public async Task<string> ObterDadosAsync(string url)
{
    using var client = new HttpClient();
    var resposta = await client.GetAsync(url);
    return await resposta.Content.ReadAsStringAsync();
}

public async Task ProcessarAsync()
{
    var dados = await ObterDadosAsync("https://api.example.com/dados");
    Console.WriteLine(dados);
}

// Uso
await ProcessarAsync();
```

### Task vs Task<T>
```csharp
// Sem retorno
public async Task ExecutarAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("Pronto");
}

// Com retorno
public async Task<int> ObterValorAsync()
{
    await Task.Delay(1000);
    return 42;
}

// Uso
await ExecutarAsync();
int valor = await ObterValorAsync();
```

### Multiple Awaits
```csharp
public async Task ProcessarVariosAsync()
{
    // Sequencial
    var resultado1 = await ObterAsync(1);
    var resultado2 = await ObterAsync(2);
    
    // Paralelo
    var task1 = ObterAsync(1);
    var task2 = ObterAsync(2);
    var resultados = await Task.WhenAll(task1, task2);
    
    // Aguardar primeiro a terminar
    var primeiro = await Task.WhenAny(task1, task2);
}

private async Task<int> ObterAsync(int id)
{
    await Task.Delay(1000);
    return id;
}
```

### Try-Catch em Async
```csharp
public async Task TentarAsync()
{
    try
    {
        var dados = await ObterDadosAsync();
    }
    catch (HttpRequestException ex)
    {
        Console.WriteLine($"Erro HTTP: {ex.Message}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Erro: {ex.Message}");
    }
}

private async Task<string> ObterDadosAsync()
{
    throw new HttpRequestException("Conexão falhou");
}
```

---

## Generics

Generics permitem criar classes, interfaces e métodos que funcionam com vários tipos.

### Classes Genéricas
```csharp
public class Repositorio<T>
{
    private List<T> _dados = new();
    
    public void Adicionar(T item) => _dados.Add(item);
    public void Remover(T item) => _dados.Remove(item);
    public List<T> ObterTodos() => _dados;
}

// Uso
var repoInt = new Repositorio<int>();
repoInt.Adicionar(10);

var repoString = new Repositorio<string>();
repoString.Adicionar("hello");
```

### Métodos Genéricos
```csharp
public class Conversor
{
    public T Converter<T>(string valor)
    {
        return (T)Convert.ChangeType(valor, typeof(T));
    }
}

// Uso
var conversor = new Conversor();
int numero = conversor.Converter<int>("42");
double decimal_ = conversor.Converter<double>("3.14");
```

### Constraints
```csharp
// T deve ser uma classe
public class Repositorio<T> where T : class
{
    public void Salvar(T item) { }
}

// T deve ter um construtor sem parâmetros
public class Factory<T> where T : new()
{
    public T Criar() => new T();
}

// T deve herdar de uma classe base
public class Servico<T> where T : EntidadeBase
{
    public void Processar(T entidade) { }
}

// T deve implementar uma interface
public class Validador<T> where T : IValidavel
{
    public bool Validar(T item) => item.EhValido();
}

// Multiple constraints
public class Processor<T> 
    where T : class, IEntidade, new()
{
}
```

---

## Reflection

Reflection permite inspecionar e manipular tipos em tempo de execução.

### Obter Informações de Tipo
```csharp
public class Pessoa
{
    public string Nome { get; set; }
    public int Idade { get; set; }
    
    public void Apresentar() { }
}

var tipo = typeof(Pessoa);

// Propriedades
var propriedades = tipo.GetProperties();
foreach (var prop in propriedades)
{
    Console.WriteLine($"Propriedade: {prop.Name}");
}

// Métodos
var metodos = tipo.GetMethods();
foreach (var metodo in metodos)
{
    Console.WriteLine($"Método: {metodo.Name}");
}

// Construtor
var construtor = tipo.GetConstructor(Type.EmptyTypes);
```

### Criar Instâncias Dinamicamente
```csharp
var tipo = typeof(Pessoa);

// Via Activator
var instancia = (Pessoa)Activator.CreateInstance(tipo);

// Via construtor
var construtor = tipo.GetConstructor(Type.EmptyTypes);
var nova = (Pessoa)construtor.Invoke(null);
```

### Acessar Propriedades Dinamicamente
```csharp
var pessoa = new Pessoa { Nome = "Gabriel", Idade = 25 };
var tipo = pessoa.GetType();

var propNome = tipo.GetProperty("Nome");
var valor = propNome.GetValue(pessoa);  // "Gabriel"

propNome.SetValue(pessoa, "Maria");
```

---

## Delegates e Events

Delegates são tipos seguros para funções (ponteiros de função).

### Delegates
```csharp
// Declarar delegate
public delegate void ProcessarDados(string dados);

// Usar delegate
public class Processador
{
    public ProcessarDados Processar { get; set; }
    
    public void Executar(string dados)
    {
        Processar?.Invoke(dados);
    }
}

// Implementação
void ExibirDados(string dados) => Console.WriteLine(dados);
void LogarDados(string dados) => Console.WriteLine($"[LOG] {dados}");

var proc = new Processador();
proc.Processar += ExibirDados;
proc.Processar += LogarDados;

proc.Executar("Teste");
// Output:
// Teste
// [LOG] Teste
```

### Events
```csharp
public class Leilao
{
    public event EventHandler<LanceEventArgs> NovoLance;
    
    public void Receber(Lance lance)
    {
        NovoLance?.Invoke(this, new LanceEventArgs(lance));
    }
}

public class LanceEventArgs : EventArgs
{
    public Lance Lance { get; }
    public LanceEventArgs(Lance lance) => Lance = lance;
}

// Usar event
var leilao = new Leilao();
leilao.NovoLance += (s, e) => Console.WriteLine($"Novo lance: {e.Lance.Valor}");
leilao.NovoLance += (s, e) => Console.WriteLine("[EMAIL] Notificação enviada");

leilao.Receber(new Lance { Valor = 100 });
```

### Built-in Delegates
```csharp
// Action - sem retorno
Action<string> saudacao = nome => Console.WriteLine($"Olá, {nome}");
saudacao("Gabriel");

// Func - com retorno
Func<int, int, int> somar = (a, b) => a + b;
int resultado = somar(5, 3);

// Predicate - retorna bool
Predicate<int> ehPar = n => n % 2 == 0;
bool resultado2 = ehPar(4);
```

---

## LINQ

Language Integrated Query permite fazer consultas em coleções.

### LINQ to Objects
```csharp
List<int> numeros = new() { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// Sintaxe de método
var pares = numeros
    .Where(n => n % 2 == 0)
    .Select(n => n * 2)
    .OrderBy(n => n)
    .ToList();

// Sintaxe de query
var paresQuery = from n in numeros
                 where n % 2 == 0
                 select n * 2;

// Exemplos comuns
var primeiro = numeros.First();                    // 1
var primeiroOuPadrao = numeros.FirstOrDefault();   // 1
var ultimo = numeros.Last();                       // 10
var contem = numeros.Contains(5);                  // true
var quantidade = numeros.Count(n => n > 5);       // 5
var soma = numeros.Sum();                          // 55
var media = numeros.Average();                     // 5.5
var minimo = numeros.Min();                        // 1
var maximo = numeros.Max();                        // 10
var distinto = numeros.Distinct().ToList();        // Remove duplicatas
var inverso = numeros.Reverse().ToList();          // Inverte ordem
```

### LINQ com Objetos
```csharp
public class Pessoa
{
    public string Nome { get; set; }
    public int Idade { get; set; }
}

List<Pessoa> pessoas = new()
{
    new Pessoa { Nome = "Gabriel", Idade = 25 },
    new Pessoa { Nome = "Maria", Idade = 30 },
    new Pessoa { Nome = "João", Idade = 22 }
};

var maiores = pessoas
    .Where(p => p.Idade >= 25)
    .Select(p => p.Nome)
    .ToList();

var agrupado = pessoas
    .GroupBy(p => p.Idade)
    .Select(g => new { Idade = g.Key, Quantidade = g.Count() })
    .ToList();

var ordenado = pessoas
    .OrderByDescending(p => p.Idade)
    .ThenBy(p => p.Nome)
    .ToList();
```

### LINQ to Entities
```csharp
// Com Entity Framework
public class RepositorioUsuario
{
    private readonly DbContext _context;
    
    public RepositorioUsuario(DbContext context) => _context = context;
    
    public List<Usuario> ObterAtivos()
    {
        return _context.Usuarios
            .Where(u => u.Ativo)
            .OrderBy(u => u.Nome)
            .ToList();  // ToList() executa no banco
    }
    
    public Usuario ObterPorEmail(string email)
    {
        return _context.Usuarios
            .FirstOrDefault(u => u.Email == email);
    }
}
```

---

## Entity Framework Core

ORM (Object-Relational Mapping) para acesso a banco de dados.

### DbContext
```csharp
public class AuctionDbContext : DbContext
{
    public DbSet<Leilao> Leiloes { get; set; }
    public DbSet<Lance> Lances { get; set; }
    public DbSet<Usuario> Usuarios { get; set; }
    
    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        options.UseSqlServer("connection-string");
    }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configurações de entidades
    }
}
```

### Entidades
```csharp
[Table("Leiloes")]
public class Leilao
{
    [Key]
    public int Id { get; set; }
    
    [Required]
    [StringLength(200)]
    public string Titulo { get; set; }
    
    [Column("data_criacao")]
    public DateTime DataCriacao { get; set; }
    
    // Relacionamentos
    public ICollection<Lance> Lances { get; set; } = new List<Lance>();
}

public class Lance
{
    public int Id { get; set; }
    public decimal Valor { get; set; }
    
    [ForeignKey(nameof(Leilao))]
    public int LeilaoId { get; set; }
    public Leilao Leilao { get; set; }
}
```

### Fluent API
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Leilao>()
        .HasKey(l => l.Id);
    
    modelBuilder.Entity<Leilao>()
        .Property(l => l.Titulo)
        .IsRequired()
        .HasMaxLength(200);
    
    modelBuilder.Entity<Leilao>()
        .HasMany(l => l.Lances)
        .WithOne(ln => ln.Leilao)
        .OnDelete(DeleteBehavior.Cascade);
}
```

### Operações CRUD
```csharp
public class RepositorioLeilao
{
    private readonly AuctionDbContext _context;
    
    public RepositorioLeilao(AuctionDbContext context) => _context = context;
    
    // Create
    public void Adicionar(Leilao leilao)
    {
        _context.Leiloes.Add(leilao);
        _context.SaveChanges();
    }
    
    // Read
    public Leilao ObterPorId(int id)
    {
        return _context.Leiloes
            .Include(l => l.Lances)
            .FirstOrDefault(l => l.Id == id);
    }
    
    // Update
    public void Atualizar(Leilao leilao)
    {
        _context.Leiloes.Update(leilao);
        _context.SaveChanges();
    }
    
    // Delete
    public void Remover(int id)
    {
        var leilao = _context.Leiloes.Find(id);
        _context.Leiloes.Remove(leilao);
        _context.SaveChanges();
    }
}
```

### Migrations
```bash
# Criar migration
dotnet ef migrations add AdicionarTabelaLeilao

# Aplicar migrations
dotnet ef database update

# Remover última migration
dotnet ef migrations remove
```

---

## Resumo

Conceitos avançados dominados:

✅ SOLID Principles para design orientado a objetos
✅ Padrões de design (Singleton, Factory, Strategy, Observer)
✅ Dependency Injection e IoC containers
✅ Programação assíncrona com Async/Await
✅ Generics e type safety
✅ Reflection para inspeção de tipos
✅ Delegates e Events para programação reativa
✅ LINQ para consultas elegantes
✅ Entity Framework Core para persistência

Dominar estes conceitos o torna um desenvolvedor C#/.NET profissional e preparado para arquiteturas escaláveis.
