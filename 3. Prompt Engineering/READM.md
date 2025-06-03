# Prompt Engineering

# Zero Shot

Tentativa de obter uma resposta da IA através de uma descrição simples e sem exemplos anteriores.

## Tricks and Tips

### Instruções claras
* Ruim: Analise esse código
* Bom: Explique o que esse código go faz e liste possíveis problemas de performance

### Linguagem declarativa e orientada a tarefa
Evite perguntas e use comandos: Liste os principais motivos para problemas de memória na linguagem Go

### Sinalização do formato esperado
Responda em forma de tópicos ou escreva 3 parágrafos

### In-Context Instruction Learning
* ruim: explique o que é uma go routine
* bom: Você é um especialista em Go. Escreva dois parágrafos explicando o que é uma go routine, como ela é usada e quais são suas limitações. Seja claro, técnico e direto.

# One Shot / Few Shot

Você passa exemplos para treinar a IA através do seu prompt

Exemplo 1
Entrada: <sua entrada>
Saída: <o que você espera>

Exemplo 2
Entrada: <sua entrada>
Saída: <o que você espera>