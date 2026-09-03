# Arquitetura As-Is: Ambiente de Desenvolvimento de Software

Documento de arquitetura estruturado nos **4 níveis do Modelo C4**, elaborado com base nas diretrizes de ambientes de desenvolvimento de software (definição, componentes, processo de setup e comparativo IDE vs. Ferramentas Independentes).

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

## Nível 4: Diagrama de Execução / Processos (Code)

O Nível 4 demonstra o fluxo sequencial de interação entre componentes ao executar e depurar software, comparando a abordagem integrada com a abordagem por ferramentas independentes via CLI.

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Desenvolvedor
    participant IDE as IDE / Editor
    participant Git as Git CLI
    participant DepManager as Gerenciador Dep.
    participant Runtime as Runtime / OS

    rect rgb(35, 50, 70)
        note over Dev, Runtime: Cenário A: Abordagem com IDE Integrada (Automática)
        Dev->>IDE: Clica em "Run/Debug"
        IDE->>Git: Valida branch ativa
        IDE->>DepManager: Resolve bibliotecas ausentes
        IDE->>Runtime: Executa processo com depurador ativo
        Runtime-->>IDE: Retorna saída do console e breakpoints
    end

    rect rgb(50, 40, 30)
        note over Dev, Runtime: Cenário B: Abordagem Independentes (Manual/CLI)
        Dev->>Git: Executa 'git checkout main' no terminal
        Dev->>DepManager: Executa 'npm install' ou 'mvn install'
        Dev->>Runtime: Executa scripts via CLI diretamente
        Runtime-->>Dev: Retorna saída bruta no terminal
    end
```

---

## Instruções de Uso

### 1. No GitHub
Basta salvar este arquivo como `README.md` ou `arquitetura-as-is.md` no seu repositório. O GitHub renderiza automaticamente os blocos `` ```mermaid `` nativamente.

### 2. No VS Code
Para visualizar e editar interativamente no VS Code:
1. Abra este arquivo (`.md`) no VS Code.
2. Instale a extensão **Markdown Preview Mermaid Support** (se usar a visualização padrão do Markdown) ou **Mermaid Preview**.
3. Pressione `Ctrl + Shift + V` (Windows/Linux) ou `Cmd + Shift + V` (macOS) para abrir o Preview nativo do VS Code.
