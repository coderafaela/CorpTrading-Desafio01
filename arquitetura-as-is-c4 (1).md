# Arquitetura As-Is: Ambiente de Desenvolvimento de Software

Documento de arquitetura nos **4 níveis do Modelo C4**, otimizado para **alto contraste de cores e legibilidade de textos** no VS Code e GitHub.

---

## Nível 1: Diagrama de Contexto (System Context)

```mermaid
graph TD
    %% Estilos de alto contraste
    classDef actor fill:#0d2538,stroke:#007acc,stroke-width:2px,color:#ffffff;
    classDef system fill:#0f4c81,stroke:#3b82f6,stroke-width:2px,color:#ffffff;
    classDef external fill:#f1f5f9,stroke:#475569,stroke-width:2px,color:#0f172a;

    Dev["<b>Desenvolvedor</b><br/>Usuário do ambiente de desenvolvimento"]:::actor
    DevEnv["<b>Ambiente de Desenvolvimento Local</b><br/>Configuração de hardware e software para criação, teste e deploy"]:::system
    RemoteVCS["<b>Plataforma de Versionamento Remota</b><br/>GitHub / GitLab / Bitbucket"]:::external
    PkgManager["<b>Repositórios de Pacotes</b><br/>npm / Maven Central / PyPI"]:::external

    Dev -->|Escreve código, roda testes e faz debug| DevEnv
    DevEnv -->|Envia/puxa código via SSH/HTTP| RemoteVCS
    DevEnv -->|Baixa dependências| PkgManager
```

---

## Nível 2: Diagrama de Contêineres (Containers)

```mermaid
graph TD
    %% Estilos com fundo claro/saturado e texto escuro para legibilidade perfeita
    classDef container fill:#e2e8f0,stroke:#1e293b,stroke-width:2px,color:#0f172a;
    classDef ext fill:#f8fafc,stroke:#64748b,stroke-width:2px,color:#1e293b;

    subgraph DevMachine[" Estação de Trabalho / Máquina Local "]
        IDE["<b>IDE / Editor Integrado</b><br/>Interface de desenvolvimento principal"]:::container
        VCS_CLI["<b>Cliente Git (CLI / GUI)</b><br/>Gestão local de branches e commits"]:::container
        RuntimeEnv["<b>Runtime & Compiladores</b><br/>Ambiente de execução de código"]:::container
        LocalData["<b>Serviços Locais / DB</b><br/>Banco de dados ou contêineres de apoio"]:::container
    end

    RemoteRepo["<b>Repositório Remoto</b>"]:::ext

    IDE -->|Invoca comandos e extensão| VCS_CLI
    IDE -->|Compila e executa via| RuntimeEnv
    IDE -->|Conecta para testes| LocalData
    VCS_CLI -->|Sincroniza alterações| RemoteRepo

    %% Estilo do Subgraph
    style DevMachine fill:#1e293b,stroke:#38bdf8,stroke-width:2px,color:#ffffff
```

---

## Nível 3: Diagrama de Componentes (Components)

```mermaid
graph TD
    %% Cores contrastantes por módulo
    classDef comp fill:#f1f5f9,stroke:#0f172a,stroke-width:1.5px,color:#0f172a;
    classDef option fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f;

    subgraph IDEModule[" 1. Módulo IDE / Editor "]
        Editor["<b>Editor de Código</b><br/>Sintaxe e navegação"]:::comp
        Debugger["<b>Depurador Integrado</b><br/>Pontos de parada (breakpoints)"]:::comp
        Plugins["<b>Extensões / Linters</b><br/>Análise estática e formatação"]:::comp
    end

    subgraph BuildModule[" 2. Módulo de Build & Execução "]
        Deps["<b>Gerenciador de Dependências</b><br/>npm, Maven, pip"]:::comp
        BuildSys["<b>Sistema de Automação de Build</b><br/>Gradle, Webpack, Make"]:::comp
        Compiler["<b>Compilador / Interpretador</b><br/>JDK, Node.js, Python"]:::comp
    end

    subgraph ConfigModule[" 3. Fluxo de Setup & Ambientes "]
        SetupProcess["<b>Etapas de Setup</b><br/>SO, Chaves SSH, Variáveis (.env)"]:::comp
    end

    subgraph TradeOff[" 4. Escolha de Abordagem "]
        IDE_Approach["<b>IDE Integrada</b><br/>+ Tudo em um só lugar<br/>- Alto uso de RAM/CPU"]:::option
        CLI_Approach["<b>Ferramentas Independentes</b><br/>+ Leveza e flexibilidade<br/>- Alternância de janelas"]:::option
    end

    Editor --> Debugger
    Editor --> Plugins
    Deps --> BuildSys
    BuildSys --> Compiler

    %% Estilização dos containers/subgraphs
    style IDEModule fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#ffffff
    style BuildModule fill:#0f172a,stroke:#34d399,stroke-width:2px,color:#ffffff
    style ConfigModule fill:#0f172a,stroke:#a78bfa,stroke-width:2px,color:#ffffff
    style TradeOff fill:#0f172a,stroke:#fbbf24,stroke-width:2px,color:#ffffff
```

---

## Nível 4: Diagrama de Execução & Fluxo de Código

```mermaid
sequenceDiagram
    autonumber
    actor Dev as 👨‍💻 Desenvolvedor
    participant IDE as 🖥️ IDE Integrada (GUI)
    participant Term as 💻 Terminal CLI
    participant Git as 🌿 Git (VCS)
    participant Dep as 📦 Gerenciador Dep.
    participant Runtime as ⚙️ Runtime / OS

    %% CENÁRIO A: Fundo claro/azulado de alto contraste
    rect rgb(240, 246, 255)
        note over Dev, Runtime: 🔵 CENÁRIO A: Fluxo Unificado via IDE Integrada (Visual & Automatizado)
        
        Dev->>+IDE: 1. Ação de Debug / Build (Clique no Botão "Run")
        
        IDE->>+Git: 2. Verifica status do repositório / Branch ativa
        Git-->>-IDE: Confirmação de status (Clean/OK)
        
        IDE->>+Dep: 3. Resolução automática de dependências
        Dep-->>-IDE: Pacotes verificados / atualizados
        
        IDE->>+Runtime: 4. Inicializa processo com depurador ativo (Breakpoints)
        activate Runtime
        Runtime-->>IDE: 5. Transmite logs de console em tempo real e intercepta breakpoints
        deactivate Runtime
        
        IDE-->>-Dev: 6. Exibe estado da aplicação, variáveis em memória e stack trace
    end

    %% CENÁRIO B: Fundo suave alaranjado de alto contraste
    rect rgb(255, 247, 237)
        note over Dev, Runtime: 🟠 CENÁRIO B: Fluxo Desacoplado via CLI (Manual & Multi-Janela)
        
        Dev->>+Term: 1. Executa 'git checkout feature & git status'
        Term->>+Git: Consulta estado local
        Git-->>-Term: Retorna branch e alterações
        Term-->>-Dev: Exibe resultado no terminal
        
        Dev->>+Term: 2. Executa 'npm install' / 'mvn clean install'
        Term->>+Dep: Baixa e resolve dependências
        Dep-->>-Term: Conclui instalação de pacotes
        Term-->>-Dev: Exibe log de dependências
        
        Dev->>+Term: 3. Executa 'npm start' / 'java -jar app.jar'
        Term->>+Runtime: Dispara processo de execução no SO
        Runtime-->>Term: Saída de texto bruta (stdout/stderr)
        Term-->>-Dev: Exibe logs diretos na janela do terminal
    end
```

---

## Como Rodar no VS Code / GitHub

1. **No VS Code:** Aperte `Ctrl + Shift + V` (ou `Cmd + Shift + V` no Mac).
2. **No GitHub:** Envie como `README.md` ou importe no repositório.
