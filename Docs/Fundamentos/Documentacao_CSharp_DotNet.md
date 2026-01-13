# Documentação de Fundamentos - C# e .NET 8.0

## 📋 Índice
1. [Introdução](#introdução)
2. [Variáveis e Tipos de Dados](#variáveis-e-tipos-de-dados)
3. [Operadores](#operadores)
4. [Estruturas de Controle](#estruturas-de-controle)
5. [Métodos](#métodos)
6. [Orientação a Objetos](#orientação-a-objetos)
7. [Coleções](#coleções)
8. [Namespaces](#namespaces)
9. [Tratamento de Exceções](#tratamento-de-exceções)

---

## Introdução

### O que é C#?
C# é uma linguagem de programação moderna, orientada a objetos e type-safe, desenvolvida pela Microsoft. É a linguagem principal para desenvolvimento na plataforma .NET.

### O que é .NET 8.0?
.NET 8.0 é uma plataforma de desenvolvimento de código aberto mantida pela Microsoft e comunidade .NET. Permite criar aplicações:
- Web (ASP.NET Core)
- Desktop (WinForms, WPF)
- Mobile (MAUI)
- Cloud
- Jogos (Unity)

**Características do .NET 8.0:**
- Versão LTS (Long-Term Support) até novembro de 2026
- Performance aprimorada
- Melhorias em garbage collection
- Novos recursos de linguagem
- Suporte a múltiplas plataformas (Windows, Linux, macOS)

---

## Variáveis e Tipos de Dados

### Tipos Primitivos

#### Inteiros
```csharp
byte b = 255;           // 0 a 255
sbyte sb = -128;        // -128 a 127
short s = 32767;        // -32768 a 32767
ushort us = 65535;      // 0 a 65535
int i = 2147483647;     // -2.1B a 2.1B (padrão)
uint ui = 4294967295;   // 0 a 4.2B
long l = 9223372036854775807;  // -9.2E18 a 9.2E18
ulong ul = 18446744073709551615; // 0 a 1.8E19
```

#### Ponto Flutuante
```csharp
float f = 3.14f;        // 32 bits, precisão ~7 dígitos
double d = 3.14159;     // 64 bits, precisão ~15-17 dígitos (padrão)
decimal dec = 123.45m;  // 128 bits, precisão ~28 dígitos (valores monetários)
```

#### Booleano e Caractere
```csharp
bool verdadeiro = true;
bool falso = false;
char c = 'A';           // Único caractere Unicode
```

#### String
```csharp
string texto = "Olá, Mundo!";
string vazio = "";
string nulo = null;     // String vazia vs null
```

### Tipos Anuláveis (Nullable Types)

```csharp
int? numero = null;     // Inteiro que pode ser nulo
string? texto = null;   // String que pode ser nula

// Verificação
if (numero.HasValue)
{
    int valor = numero.Value;
}

// Operador coalescente
int resultado = numero ?? 0;  // Se null, usa 0
```

### Inferência de Tipo (var)

```csharp
var numero = 42;        // var é int
var texto = "Hello";    // var é string
var lista = new List<int>(); // var é List<int>
```

### Constantes

```csharp
const int MAXVALOR = 100;
const string NOME = "RocketSeat";

// Readonly - constante em tempo de execução
private readonly string _configuracao;
```

---

## Operadores

### Aritméticos
```csharp
int a = 10, b = 3;
int soma = a + b;           // 13
int diferenca = a - b;      // 7
int produto = a * b;        // 30
int quociente = a / b;      // 3
int resto = a % b;          // 1
int potencia = (int)Math.Pow(a, 2);  // 100
```

### Comparação
```csharp
int x = 5, y = 10;
bool igual = x == y;        // false
bool diferente = x != y;    // true
bool maior = x > y;         // false
bool maiorIgual = x >= y;   // false
bool menor = x < y;         // true
bool menorIgual = x <= y;   // true
```

### Lógicos
```csharp
bool a = true, b = false;
bool e = a && b;            // false (AND)
bool ou = a || b;           // true (OR)
bool negacao = !a;          // false (NOT)
```

### Atribuição
```csharp
int x = 10;
x += 5;     // x = x + 5 → 15
x -= 3;     // x = x - 3 → 12
x *= 2;     // x = x * 2 → 24
x /= 4;     // x = x / 4 → 6
x %= 5;     // x = x % 5 → 1
```

### Ternário
```csharp
int idade = 20;
string categoria = idade >= 18 ? "Adulto" : "Menor";
```

---

## Estruturas de Controle

### If-Else
```csharp
int nota = 75;

if (nota >= 90)
{
    Console.WriteLine("Excelente");
}
else if (nota >= 70)
{
    Console.WriteLine("Bom");
}
else if (nota >= 50)
{
    Console.WriteLine("Satisfatório");
}
else
{
    Console.WriteLine("Reprovado");
}
```

### Switch
```csharp
int dia = 3;
string nomeDia = dia switch
{
    1 => "Segunda",
    2 => "Terça",
    3 => "Quarta",
    4 => "Quinta",
    5 => "Sexta",
    6 => "Sábado",
    7 => "Domingo",
    _ => "Inválido"
};

// Switch tradicional
switch (dia)
{
    case 1:
    case 2:
    case 3:
    case 4:
    case 5:
        Console.WriteLine("Dia útil");
        break;
    case 6:
    case 7:
        Console.WriteLine("Fim de semana");
        break;
    default:
        Console.WriteLine("Inválido");
        break;
}
```

### Loops

#### For
```csharp
for (int i = 0; i < 10; i++)
{
    Console.WriteLine(i);
}
```

#### Foreach
```csharp
int[] numeros = { 1, 2, 3, 4, 5 };
foreach (int num in numeros)
{
    Console.WriteLine(num);
}
```

#### While
```csharp
int contador = 0;
while (contador < 5)
{
    Console.WriteLine(contador);
    contador++;
}
```

#### Do-While
```csharp
int contador = 0;
do
{
    Console.WriteLine(contador);
    contador++;
} while (contador < 5);
```

### Break e Continue
```csharp
for (int i = 0; i < 10; i++)
{
    if (i == 5)
        break;      // Sai do loop
    
    if (i == 2)
        continue;   // Pula para próxima iteração
    
    Console.WriteLine(i);
}
```

---

## Métodos

### Declaração Básica
```csharp
public int Somar(int a, int b)
{
    return a + b;
}
```

### Sem Retorno (void)
```csharp
public void Exibir(string mensagem)
{
    Console.WriteLine(mensagem);
}
```

### Parâmetros Padrão
```csharp
public void Saudar(string nome = "Visitante")
{
    Console.WriteLine($"Olá, {nome}!");
}

Saudar();              // Olá, Visitante!
Saudar("Gabriel");     // Olá, Gabriel!
```

### Parâmetros Nomeados
```csharp
public void CriarPessoa(string nome, int idade)
{
    Console.WriteLine($"{nome} tem {idade} anos");
}

CriarPessoa(idade: 25, nome: "Gabriel");
```

### Parâmetros por Referência (ref)
```csharp
public void Incrementar(ref int valor)
{
    valor++;
}

int x = 5;
Incrementar(ref x);  // x agora é 6
```

### Out Parameter
```csharp
public bool TentarParsear(string entrada, out int resultado)
{
    return int.TryParse(entrada, out resultado);
}

if (TentarParsear("42", out int numero))
{
    Console.WriteLine(numero);
}
```

### Params (Argumentos Variáveis)
```csharp
public int Somar(params int[] numeros)
{
    int total = 0;
    foreach (int num in numeros)
        total += num;
    return total;
}

int resultado = Somar(1, 2, 3, 4, 5);  // 15
```

---

## Orientação a Objetos

### Classes e Objetos

#### Definição de Classe
```csharp
public class Pessoa
{
    // Campo (variável de classe)
    private string _nome;
    
    // Propriedade com auto-implementação
    public int Idade { get; set; }
    
    // Propriedade com lógica
    public string Nome
    {
        get { return _nome; }
        set { _nome = value; }
    }
    
    // Construtor
    public Pessoa(string nome, int idade)
    {
        _nome = nome;
        Idade = idade;
    }
    
    // Método
    public void Apresentar()
    {
        Console.WriteLine($"Olá, sou {_nome} e tenho {Idade} anos");
    }
}
```

#### Criação de Instâncias
```csharp
Pessoa p1 = new Pessoa("Gabriel", 25);
Pessoa p2 = new("Maria", 30);  // Target-typed new (C# 9.0+)

p1.Apresentar();
```

### Encapsulamento

Access Modifiers:
```csharp
public class Exemplo
{
    public int publico;              // Acessível em qualquer lugar
    private int privado;             // Acessível apenas na classe
    protected int protegido;         // Acessível na classe e herança
    internal int interno;            // Acessível no mesmo assembly
    protected internal int misturado; // protected OR internal
}
```

### Herança

```csharp
public class Animal
{
    public string Nome { get; set; }
    
    public virtual void Fazer Barulho()
    {
        Console.WriteLine("Som genérico");
    }
}

public class Cachorro : Animal
{
    public override void FazerBarulho()
    {
        Console.WriteLine("Au au!");
    }
}

Animal animal = new Cachorro { Nome = "Rex" };
animal.FazerBarulho();  // Au au!
```

### Polimorfismo

```csharp
public interface IVeiculo
{
    void Acelerar();
    void Frear();
}

public class Carro : IVeiculo
{
    public void Acelerar() => Console.WriteLine("Carro acelerando");
    public void Frear() => Console.WriteLine("Carro freando");
}

public class Moto : IVeiculo
{
    public void Acelerar() => Console.WriteLine("Moto acelerando");
    public void Frear() => Console.WriteLine("Moto freando");
}

List<IVeiculo> veiculos = new()
{
    new Carro(),
    new Moto()
};

foreach (var veiculo in veiculos)
{
    veiculo.Acelerar();
}
```

### Classes Abstratas

```csharp
public abstract class Forma
{
    public abstract double CalcularArea();
    
    public void Descrever()
    {
        Console.WriteLine($"Área: {CalcularArea()}");
    }
}

public class Circulo : Forma
{
    private double _raio;
    
    public Circulo(double raio) => _raio = raio;
    
    public override double CalcularArea()
    {
        return Math.PI * _raio * _raio;
    }
}
```

---

## Coleções

### Array
```csharp
int[] numeros = new int[5];           // Array de 5 elementos
int[] valores = { 1, 2, 3, 4, 5 };   // Inicialização
string[] nomes = new string[3];       // Array de strings
```

### List<T>
```csharp
List<int> numeros = new();            // Inicializar vazio
List<int> valores = new() { 1, 2, 3 };

numeros.Add(10);                       // Adicionar
numeros.AddRange(new[] { 11, 12 });  // Adicionar vários
numeros.Remove(10);                    // Remover
numeros.RemoveAt(0);                   // Remover por índice
int primeiro = numeros[0];             // Acessar elemento
int count = numeros.Count;             // Quantidade
numeros.Clear();                       // Limpar
```

### Dictionary<K, V>
```csharp
Dictionary<string, int> idades = new()
{
    { "Gabriel", 25 },
    { "Maria", 30 },
    { "João", 28 }
};

idades.Add("Ana", 26);
int idadeGabriel = idades["Gabriel"];
bool existe = idades.ContainsKey("Gabriel");
idades.Remove("João");
```

### HashSet<T>
```csharp
HashSet<int> numeros = new() { 1, 2, 3, 3, 4 };
// Garante unicidade: { 1, 2, 3, 4 }

numeros.Add(5);
bool contem = numeros.Contains(3);
```

### Queue<T> e Stack<T>
```csharp
// Fila (FIFO - First In, First Out)
Queue<int> fila = new();
fila.Enqueue(1);
fila.Enqueue(2);
int primeiro = fila.Dequeue();  // 1

// Pilha (LIFO - Last In, First Out)
Stack<int> pilha = new();
pilha.Push(1);
pilha.Push(2);
int ultimo = pilha.Pop();       // 2
```

---

## Namespaces

### Declaração e Uso
```csharp
namespace RocketSeat.Auction.Domain
{
    public class Leilao
    {
        public string Titulo { get; set; }
    }
}

// Usando o namespace
using RocketSeat.Auction.Domain;

Leilao leilao = new() { Titulo = "Meu Leilão" };
```

### Using Global
```csharp
// Arquivo: GlobalUsings.cs
global using System;
global using System.Collections.Generic;
global using RocketSeat.Auction.Domain;
```

---

## Tratamento de Exceções

### Try-Catch-Finally
```csharp
try
{
    int resultado = 10 / int.Parse("0");
}
catch (DivideByZeroException ex)
{
    Console.WriteLine($"Erro: {ex.Message}");
}
catch (FormatException ex)
{
    Console.WriteLine($"Erro de formato: {ex.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"Erro geral: {ex.Message}");
}
finally
{
    Console.WriteLine("Execução finalizada");
}
```

### Throwing Exception
```csharp
public void ValidarIdade(int idade)
{
    if (idade < 0)
        throw new ArgumentException("Idade não pode ser negativa");
    
    if (idade > 150)
        throw new ArgumentOutOfRangeException(nameof(idade));
}
```

### Custom Exception
```csharp
public class LeilaoException : Exception
{
    public LeilaoException(string mensagem) : base(mensagem) { }
}

throw new LeilaoException("Leilão não encontrado");
```

### Using Statement (Disposed automático)
```csharp
using (var arquivo = File.OpenRead("dados.txt"))
{
    // Usar arquivo
} // Dispose é chamado automaticamente

// Declaração using (C# 8.0+)
using var arquivo2 = File.OpenRead("dados.txt");
// Dispose é chamado ao fim do método/bloco
```

---

## Resumo

Este documento apresentou os fundamentos essenciais de C# e .NET 8.0:

✅ Tipos de dados e variáveis
✅ Operadores e expressões
✅ Estruturas de controle de fluxo
✅ Métodos e funções
✅ Programação orientada a objetos
✅ Coleções e estruturas de dados
✅ Organização com namespaces
✅ Tratamento robusto de exceções

Dominando estes conceitos, você estará pronto para aprender padrões avançados e arquiteturas profissionais em C# e .NET.
