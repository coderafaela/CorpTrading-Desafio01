---

## Nível 1: Diagrama de Contexto (System Context)

O Nível 1 apresenta a visão geral do sistema em relação aos atores (desenvolvedores) e serviços externos envolvidos (repositórios de código e gerenciadores de pacotes).

```mermaid
graph TD
    classDef actor fill:#2b3a4a,stroke:#4a6fa5,stroke-width:2px,color:#fff;
    classDef system fill:#1f2d3d,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef external fill:#374151,stroke:#9ca3af,stroke-width:1px,color:#fff;

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

O Nível 2 detalha as grandes unidades funcionais (softwares/sistemas) que compõem o ambiente de desenvolvimento local na máquina do desenvolvedor.

```mermaid
graph TD
    classDef container fill:#1f2d3d,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef ext fill:#374151,stroke:#9ca3af,stroke-width:1px,color:#fff;

    subgraph DevMachine["Estação de Trabalho / Máquina Local"]
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
```

---

## Nível 3: Diagrama de Componentes (Components)

O Nível 3 detalha os módulos e subsistemas específicos internos de cada ferramenta, cobrindo edição, build, fluxo de instalação e o comparativo das abordagens.

```mermaid
graph TD
    classDef comp fill:#2d3748,stroke:#10b981,stroke-width:2px,color:#fff;
    classDef option fill:#374151,stroke:#f59e0b,stroke-width:1px,color:#fff;

    subgraph IDEModule["1. Módulo IDE / Editor"]
        Editor["<b>Editor de Código</b><br/>Sintaxe e navegação"]:::comp
        Debugger["<b>Depurador Integrado</b><br/>Pontos de parada (breakpoints)"]:::comp
        Plugins["<b>Extensões / Linters</b><br/>Análise estática e formatação"]:::comp
    end

    subgraph BuildModule["2. Módulo de Build & Execução"]
        Deps["<b>Gerenciador de Dependências</b><br/>npm, Maven, pip"]:::comp
        BuildSys["<b>Sistema de Automação de Build</b><br/>Gradle, Webpack, Make"]:::comp
        Compiler["<b>Compilador / Interpretador</b><br/>JDK, Node.js, Python"]:::comp
    end

    subgraph ConfigModule["3. Fluxo de Setup & Ambientes"]
        SetupProcess["<b>Etapas de Setup</b><br/>SO, Chaves SSH, Variáveis de Ambiente (.env)"]:::comp
    end

    subgraph TradeOff["4. Escolha de Abordagem"]
        IDE_Approach["<b>IDE Integrada</b><br/>+ Tudo em um só lugar<br/>- Alto uso de RAM/CPU"]:::option
        CLI_Approach["<b>Ferramentas Independentes</b><br/>+ Leveza e flexibilidade<br/>- Alternância de janelas"]:::option
    end

    Editor --> Debugger
    Editor --> Plugins
    Deps --> BuildSys
    BuildSys --> Compiler
```

---

## Nível 4: Diagrama de Execução & Fluxo de Código (Otimizado)

O Nível 4 foi reestruturado com **blocos visuais de ativação**, **distinção clara entre o fluxo de IDE Integrada e CLI Independente**, e **rótulos explicativos**.

```mermaid
sequenceDiagram
    autonumber
    actor Dev as 👨‍💻 Desenvolvedor
    participant IDE as 🖥️ IDE Integrada (GUI)
    participant Term as 💻 Terminal CLI
    participant Git as 🌿 Git (VCS)
    participant Dep as 📦 Gerenciador Dep.
    participant Runtime as ⚙️ Runtime / OS

    %% ==========================================
    %% CENÁRIO 1: ABORDAGEM IDE INTEGRADA
    %% ==========================================
    rect rgb(30, 41, 59)
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

    %% ==========================================
    %% CENÁRIO 2: ABORDAGEM FERRAMENTAS INDEPENDENTES
    %% ==========================================
    rect rgb(45, 30, 20)
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

## Instruções de Uso

### 1. No GitHub
Basta salvar este arquivo como `README.md` ou `arquitetura-as-is.md` no seu repositório. O GitHub renderiza automaticamente os blocos `` ```mermaid `` nativamente.

### 2. No VS Code
Para visualizar e editar interativamente no VS Code:
1. Abra este arquivo (`.md`) no VS Code.
2. Instale a extensão **Markdown Preview Mermaid Support** (se usar a visualização padrão do Markdown) ou **Mermaid Preview**.
3. Pressione `Ctrl + Shift + V` (Windows/Linux) ou `Cmd + Shift + V` (macOS) para abrir o Preview nativo do VS Code.
