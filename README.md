# 🧮 Cryo Calculator

Calculadora científica escrita em **[Cryo](https://github.com/Victor-477/Cryo)**, com toda a matemática vinda da biblioteca **[Cryo-Math](https://github.com/Victor-477/Cryo-Math)**.

```text
=== Cryo Calculator ===
Digite uma expressao, 'help' para ajuda ou 'sair' para encerrar.
> 2 * sin(pi/6) + 3^2
= 10
> x = 3.5
= 3.5
> 2x + 1
= 8
> 0.1 + 0.2
= 0.3
> deg
modo: graus
> cos(60)
= 0.5
> sqrt(-1)
= Erro: sqrt: raiz de numero negativo
```

## Recursos

- Expressões com precedência correta: `+ - * / % ^ !` e parênteses. `^` associa à direita (`2^3^2 = 512`), e `-2^2 = -4`.
- **Multiplicação implícita**: `2pi`, `3(4 + 1)`, `2 sqrt(9)`, `(1+1)(2+2)`.
- Números decimais e em notação científica: `42`, `3.14`, `.5`, `1.5e3`, `2E-4`.
- Constantes `pi`, `e`, `tau`, `phi` e `ans` (último resultado).
- **Variáveis**: `x = 3.5`, e depois `x^2 + 1`.
- Modo de ângulo em **graus** ou **radianos** (`deg` / `rad`).
- Mais de 50 funções: trigonométricas, hiperbólicas, raízes, logaritmos, arredondamento, combinatória, MDC/MMC, primalidade e estatística com vários argumentos.
- Resultados com 12 algarismos significativos, então `0.1 + 0.2` mostra `0.3`.
- Mensagens de erro claras em vez de travar: divisão por zero, domínio inválido, sintaxe, variável ou função desconhecida.
- Histórico da sessão (`hist`) e lista de variáveis (`vars`).

### Funções

| Grupo | Funções |
|---|---|
| Trigonometria | `sin` `cos` `tan` `asin` `acos` `atan` `atan2(y, x)` |
| Hiperbólicas | `sinh` `cosh` `tanh` `asinh` `acosh` `atanh` |
| Raízes | `sqrt` (ou `raiz`) `cbrt` `root(x, n)` `hypot(a, b)` |
| Potências e logaritmos | `pow(x, y)` `exp` `ln` `log` (base 10) `log(x, base)` `log2` |
| Arredondamento | `abs` `sign` `floor` `ceil` `round` `round(x, casas)` `trunc` `frac` |
| Inteiros | `fact` (ou `n!`) `fib` `gcd`/`mdc` `lcm`/`mmc` `ncr(n, k)` `npr(n, k)` `mod(a, b)` `isprime` |
| Estatística (n argumentos) | `sum` `prod` `mean`/`avg` `median` `stddev` `var` `min` `max` |
| Outros | `percent(valor, pct)` `rad(graus)` `deg(radianos)` |

### Comandos

| Comando | Ação |
|---|---|
| `help` | mostra a ajuda |
| `deg` / `rad` | muda o modo de ângulo |
| `vars` | lista as variáveis |
| `hist` | histórico da sessão |
| `clear` | apaga variáveis e `ans` |
| `sair` / `q` / `quit` / `exit` | encerra |

## Como rodar

### Pré-requisitos

- Python 3.11+ (o compilador lê o `cryo.toml` com `tomllib`)
- Go **ou** gcc, para compilar a Pyro VM
- Os repositórios do ecossistema clonados **lado a lado**:

```text
pasta/
├── burnout/            https://github.com/Victor-477/Burnout
├── cryo/               https://github.com/Victor-477/Cryo   (branch dev)
├── pyro/               https://github.com/Victor-477/pyro
├── Cryo-Math/          https://github.com/Victor-477/Cryo-Math
└── Cryo_Calculator/    este repositório
```

```bash
git clone https://github.com/Victor-477/Burnout.git burnout
git clone -b dev https://github.com/Victor-477/Cryo.git cryo
git clone https://github.com/Victor-477/pyro.git pyro
git clone https://github.com/Victor-477/Cryo-Math.git
git clone https://github.com/Victor-477/Cryo_Calculator.git
```

A dependência da Cryo-Math é declarada no [`cryo.toml`](cryo.toml) como `cryo_math = "../Cryo-Math"`, por isso as duas pastas precisam ficar uma ao lado da outra.

### Modo interativo

```bash
python burnout/cryoc.py Cryo_Calculator/main.cryo --backend pyro --run
```

### Modo linha de comando

Compile uma vez e passe a expressão como argumento:

```bash
python burnout/cryoc.py Cryo_Calculator/main.cryo --backend pyro -o calc.pyro
build/pyrovm calc.pyro "2 * sin(pi/6) + 3^2"      # 10
```

### Outros backends

O mesmo código roda em `--backend node` e `--backend go`:

```bash
python burnout/cryoc.py Cryo_Calculator/main.cryo --backend go -o calc.go --emit-only
```

## Testes

```bash
python burnout/cryoc.py Cryo_Calculator/tests/test_calc.cryo --backend pyro --run
```

São 98 casos: precedência, multiplicação implícita, funções, variáveis, modo graus e erros. O código de saída é diferente de zero se algum falhar. A suíte passa nos backends `pyro`, `node` e `go`.

## Estrutura

| Arquivo | Conteúdo |
|---|---|
| [`main.cryo`](main.cryo) | programa: REPL, comandos e modo linha de comando |
| [`calc.cryo`](calc.cryo) | motor: tokenizador, parser descendente recursivo e avaliação |
| [`cryo.toml`](cryo.toml) | manifesto do pacote e dependência da Cryo-Math |
| [`tests/test_calc.cryo`](tests/test_calc.cryo) | suíte de testes |

O motor pode ser reutilizado em outro programa:

```cryo
import "calc.cryo" as calc;

print(calc::execute("hypot(3, 4) * 2"));   // 10
number v = calc::evaluate("5!");            // 120 (lança string em caso de erro)
```

## Observações

- `%` é o resto com o sinal do dividendo (`-7 % 3 = -1`). Para resto sempre positivo use `mod(-7, 3) = 2`.
- `input()` devolve `""` tanto para linha vazia quanto para fim da entrada (EOF). Por isso a calculadora encerra depois de 50 linhas vazias seguidas, o que evita um laço infinito quando a entrada vem de um arquivo ou pipe.

## Licença

MIT. Veja [LICENSE](LICENSE).
