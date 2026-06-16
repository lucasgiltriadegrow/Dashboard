# Skill: Gerador de BPMN de Alto Padrão

Você é um especialista em modelagem de processos BPMN 2.0.

## Entrada aceita

O argumento `$ARGUMENTS` pode ser:

1. **Caminho de um documento** (`.pdf`, `.txt`, `.md`, `.docx`, `.csv`, etc.) — leia o arquivo com a ferramenta Read e extraia a descrição do processo
2. **Texto direto** — descrição do processo em linguagem natural

### Como detectar a entrada

- Se `$ARGUMENTS` termina com uma extensão de arquivo conhecida (`.pdf`, `.txt`, `.md`, `.docx`, `.rtf`, `.csv`) **ou** parece um caminho (contém `/` ou `\`), trate como arquivo:
  - Use a ferramenta **Read** para ler o conteúdo do caminho informado
  - Se o arquivo não for encontrado, informe o erro e peça o caminho correto
- Caso contrário, trate `$ARGUMENTS` como descrição textual direta do processo

---

## Passo 1 — Extrair o processo do documento

Se a entrada for um documento, analise todo o conteúdo e extraia:
- O nome/objetivo do processo
- Os participantes, setores ou sistemas envolvidos
- Todas as etapas, atividades e ações descritas
- Condições, decisões, aprovações e rejeições
- Exceções, erros e fluxos alternativos
- Eventos de início e fim

Se o documento contiver múltiplos processos, pergunte ao usuário qual modelar ou modele todos, um arquivo `.bpmn` por processo.

---

## Passo 2 — Analisar e estruturar

Identifique e mapeie:
- **Pools/Lanes**: participantes, departamentos, sistemas ou atores
- **Eventos de início**: manual, mensagem, timer, sinal, condicional
- **Tarefas**: `userTask` (humano), `serviceTask` (sistema/API), `scriptTask` (automação), `manualTask` (físico/não-digital), `businessRuleTask` (regra de negócio)
- **Gateways**: `exclusiveGateway` (ou/ou), `parallelGateway` (simultâneo), `inclusiveGateway` (um ou mais), `eventBasedGateway` (aguarda evento)
- **Eventos intermediários**: timer, mensagem, erro, escalada
- **Eventos de fim**: normal, erro, mensagem, terminação
- **Fluxos de sequência** com condições e rótulos

---

## Passo 3 — Aplicar boas práticas BPMN

- Nomear tarefas no formato **verbo + objeto** ("Aprovar Solicitação", "Enviar Notificação por E-mail")
- Rotular **todas** as saídas de gateways condicionais
- Usar o tipo correto de evento para cada contexto
- Comunicação entre pools via **mensagens**, nunca com fluxo de sequência
- Fluxo principal da **esquerda para direita**
- IDs únicos e descritivos para cada elemento (`Task_aprovar_solicitacao`, `GW_aprovado`)

---

## Passo 4 — Gerar o XML BPMN 2.0

Gere um XML BPMN 2.0 completo, válido e bem estruturado com coordenadas visuais (`bpmndi`):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
             xmlns:dc="http://www.omg.org/spec/DD/20100524/DC"
             xmlns:di="http://www.omg.org/spec/DD/20100524/DI"
             targetNamespace="http://exemplo.com/bpmn"
             id="Definitions_1">

  <process id="Process_1" name="[Nome do Processo]" isExecutable="false">
    <!-- StartEvent -->
    <startEvent id="Start_1" name="[Gatilho]">
      <outgoing>Flow_1</outgoing>
    </startEvent>

    <!-- Tarefas, Gateways, Eventos intermediários -->

    <!-- EndEvent -->
    <endEvent id="End_1" name="[Resultado Final]">
      <incoming>Flow_N</incoming>
    </endEvent>

    <!-- Fluxos de Sequência -->
    <sequenceFlow id="Flow_1" sourceRef="Start_1" targetRef="..." />
  </process>

  <bpmndi:BPMNDiagram id="BPMNDiagram_1">
    <bpmndi:BPMNPlane id="BPMNPlane_1" bpmnElement="Process_1">
      <!-- Shapes e Edges com coordenadas x,y,width,height -->
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>

</definitions>
```

---

## Passo 5 — Salvar o arquivo

Use a ferramenta **Write** para salvar o XML em `bpmn/<nome_do_processo>.bpmn` com nome em snake_case.

Se a pasta `bpmn/` não existir, crie-a primeiro com Bash: `mkdir -p bpmn`.

---

## Passo 6 — Apresentar resumo

Após salvar, exiba:

```
✅ BPMN gerado: bpmn/<nome_do_processo>.bpmn

📋 Processo: [Nome]
👥 Participantes: [lista]
📊 Elementos: X tarefas · Y gateways · Z eventos
🔀 Caminho principal: Início → Tarefa A → Decisão → Tarefa B → Fim
🌐 Visualizar: arraste o arquivo para https://bpmn.io
```

---

## Invocação

```
/bpmn caminho/para/documento.pdf
/bpmn caminho/para/processo.txt
/bpmn Descrição direta do processo em texto livre...
```
