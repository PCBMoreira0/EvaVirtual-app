# EvaSim: Simulador Virtual do Robô Social Eva

O **EvaSim** é uma aplicação de simulação em 3D desenvolvida como parte de uma pesquisa voltada à criação de um robô social para auxiliar no tratamento de crianças com Transtorno do Espectro Autista (TEA).

Este projeto provê um ambiente virtual de testes e interações que simula o robô físico "Eva". Ele é totalmente integrado ao software controlador do robô original por meio de APIs, permitindo que pesquisadores e profissionais de saúde testem scripts de comportamento, fluxos de conversação e respostas emocionais antes de levá-los ao hardware real.

## Principais Recursos

A simulação busca replicar fielmente as capacidades expressivas e comunicativas do robô físico:

- **Comunicação Verbal:** Integração com serviços de conversão de texto em fala (TTS via IBM Watson) e reconhecimento de fala (STT via Google Speech Recognition).
- **Expressão Emocional Visual:** Renderização das expressões faciais do robô na tela virtual e simulação do painel de LEDs animado no peito.
- **Reconhecimento de Expressões:** Detecção e classificação de emoções faciais do usuário em tempo real utilizando *Computer Vision* (OpenCV, MediaPipe e modelos TensorFlow).
- **Movimentação:** Suporte a comandos de movimento para a cabeça.
- **Interpretação de Scripts:** Suporte nativo à linguagem **EvaML** (baseada em XML) para orquestrar rotinas complexas de interação, lógica condicional e fluxos terapêuticos.

## Arquitetura do Sistema

O projeto é dividido em duas camadas principais que se comunicam assincronamente:

### 1. Frontend (Unity 3D)

Desenvolvido em **Unity**, o cliente renderiza o modelo 3D do robô Eva e o ambiente virtual. Ele consome os eventos do backend para atualizar o estado do robô (animações corporais, troca de texturas no rosto da Eva e controle de cores/LEDs) e captura interações do usuário.

### 2. Backend (Python)

Um servidor robusto baseado em **FastAPI** e **Docker** que orquestra a inteligência do robô:

- **Orquestrador:** Cria ambientes isolados (containers Docker) para cada usuário/sessão iniciada.
- **Comunicação Bidirecional:** Utiliza **WebSockets** para comunicação cliente-servidor e um Broker **MQTT** (Paho) para rotear mensagens de comando internos.
- **Gerenciador de Mídia:** Armazena e serve arquivos de áudio dinâmicos gerados pelas interações.

## Como Executar o Projeto

### Pré-requisitos

- [Docker](https://www.docker.com/) e Docker Compose
- [Python 3.10+](https://www.python.org/)
- [Unity Editor](https://unity.com/) (para rodar ou compilar o cliente 3D)
- Credenciais do IBM Watson (para funcionalidade completa de TTS)

### Clonando o Repositório

O projeto possui um **Git submodule** que é necessário para sua execução. Para clonar o repositório juntamente com seus submódulos, utilize:

```bash
git clone --recurse-submodules https://github.com/PCBMoreira0/EvaVirtual-app.git
cd EvaVirtual-app
```

Caso o repositório já tenha sido clonado sem os submódulos, execute:
```bash
git submodule update --init --recursive
```

### Configurando o Backend

1. Crie o arquivo ibm_cred.txt dentro da pasta backend/orchestrator e coloque sua chave da IBM Watson nele.
2. Crie a imagem do simulador rodando:
```bash
cd backend
docker build -t evasim/simulator:latest -f simulator/Dockerfile simulator/
```

3. Inicie a infraestrutura e o orquestrador via Docker:

```bash
docker compose up --build
```

O orquestrador estará disponível em `http://localhost:8000`.

### Executando o Frontend (Unity)

1. Abra o diretório `frontend` no Unity Hub.
2. Na pasta `Scenes`, abra a cena principal do simulador.
3. Certifique-se de que o backend local está rodando e execute o projeto dando Play no editor (ou faça o *build* para a plataforma desejada).

## Scripts (EvaML)

O robô é controlado por meio de arquivos XML chamados de **EvaML**, localizados na pasta `backend/orchestrator/eva_scripts/`. Eles definem as rotinas da Eva e são parte do simulador do robô Eva. 