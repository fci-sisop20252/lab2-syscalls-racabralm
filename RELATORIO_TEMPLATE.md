# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
Há diferença pois a contagem é diferente nos dois métodos. O 'write' conta bytes, enquanto o 'printf' conta caracteres. 
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
O 'write' é mais previsível, pois é uma syscall de baixo nível que apenas escreve um bloco de bytes. O 'printf' interpreta strings, converte valores e lida com o buffer da biblioteca. 
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
Foi usado o 3. Não começou em 0, 1 ou 2 porque esses três file descriptors são, por padrão, reservados para a entrada padrão (0), a saída padrão (1) e a saída de erro padrão (2). O sistema operacional aloca o próximo número inteiro disponível para o arquivo que foi aberto.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
A chamada read() retornou 127, que é o número exato de bytes que o programa tentou ler (BUFFER_SIZE - 1). Isso indica que todo o conteúdo do arquivo se encaixou no buffer fornecido, sem que tivesse mais dados para ler. A impressão do conteúdo do arquivo também confirma que a leitura foi completa.
```

**3. Por que verificar retorno de cada syscall?**

```
Porque elas podem falhar. Erros como a não existência de um arquivo (open), problemas de hardware ou permissão (read), ou falhas ao liberar recursos (close) podem acontecer.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000154 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |       82        |  0.000203 |
| 64          |       21        |  0.000154 |
| 256         |        6        |  0.000091 |
| 1024        |        2        |  0.000177 |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
O tamanho do buffer e o número de syscalls read() têm uma relação inversa. À medida que o tamanho do buffer aumenta, cada chamada read() pode transferir mais dados, reduzindo o número total de chamadas necessárias para ler o arquivo inteiro. Isso minimiza o custo de comutação entre o modo de usuário e o modo de kernel.
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, a última chamada read() do loop de leitura retornou um número de bytes menor que BUFFER_SIZE. Isso ocorre porque, ao se aproximar do final do arquivo, o número de bytes restantes é, na maioria dos casos, menor do que o tamanho do buffer. O valor de retorno de read() mostra quantos bytes foram de fato lidos, e quando esse valor é zero, o loop termina.
```

**3. Qual é a relação entre syscalls e performance?**

```
Existe uma relação inversa entre o número de syscalls e a performance. Um maior número de chamadas de sistema aumenta o "overhead" de execução, pois o sistema precisa alternar repetidamente entre o modo de usuário e o modo de kernel para cada operação. Ao diminuir o número de syscalls, o programa se torna mais eficiente e rápido.

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000263 segundos
- Throughput: 5064.76 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
[Sua análise aqui]
```

**2. Que flags são essenciais no open() do destino?**

```
[Sua análise aqui]
```

**3. O número de reads e writes é igual? Por quê?**

```
[Sua análise aqui]
```

**4. Como você saberia se o disco ficou cheio?**

```
[Sua análise aqui]
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
[Sua análise aqui]
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
[Sua análise aqui]
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
[Sua análise aqui]
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
[Sua análise aqui]
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** _____

**Por que você acha que foi mais rápido?**

```
[Sua análise aqui]
```

---

## 📤 Entrega
Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
