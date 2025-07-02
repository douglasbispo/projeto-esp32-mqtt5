# Controle Remoto de LED com ESP32 via MQTT

## Visão Geral do Projeto

Este projeto foi desenvolvido com o intuito de implementar um cliente MQTT em um módulo ESP32. O objetivo principal é controlar o estado de um LED na placa ESP32 por meio de mensagens específias através de um broker MQTT, de modo que sejá possível ligar ou desligar o LED remotamente, demonstrando um sistema básico de controle e comunicação em tempo real. O ESP32 atua como um assinante (subscriber) neste tópico, recebendo comandos de uma aplicação cliente MQTT externa.

*** 

## Funcionalidades Implementadas

* **Conectividade Wi-Fi:** O ESP32 se conecta à sua rede Wi-Fi local.
* **Cliente MQTT:** Estabelece comunicação com um broker MQTT central.
* **Inscrição em Tópico Dedicado:** O ESP32 assina o tópico `/ifpe/ads/embarcados/esp32/led` para receber comandos.
* **Controle de LED Inteligente:**
    * Recebe "1" no tópico: O LED da placa acende.
    * Recebe "0" no tópico: O LED da placa apaga.
* **Monitoramento Detalhado:** Todos os eventos de conexão, mensagens MQTT e ações do LED são registrados no monitor serial para facilitar a depuração e o acompanhamento.

***

## Pré-requisitos de Hardware e Software

Para replicar este projeto, você precisará dos seguintes itens:

### Hardware

* **Placa de Desenvolvimento ESP32:** Uma placa de desenvolvimento ESP32.
* **Cabo USB-C:** Um cabo de dados USB funcional para conectar o ESP32 ao seu computador.
* **Conectividade de Rede:** Acesso a uma rede Wi-Fi de 2.4GHz.

### Software

* **ESP-IDF (Espressif IoT Development Framework):** Instale e configure em seu sistema. Para instruções de instalação, consulte o [Guia de instalação do ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html).
* **Visual Studio Code (VS Code):** Seu editor de código. Instale a [Extensão ESP-IDF](https://github.com/espressif/vscode-esp-idf-extension) para integrar as ferramentas de desenvolvimento.
* **Cliente MQTT:** Aplicação para publicar mensagens MQTT. Sugiro o [MQTT Explorer](https://mqtt-explorer.com/) por sua interface intuitiva.
* **Broker MQTT:** Um servidor MQTT acessível (por exemplo, o broker público `broker.hivemq.com` na porta 1883 para testes, ou seu próprio broker local).

***

## Como executar o projeto

Siga esses passos para colocar o projeto em funcionamento no seu ESP32.

### 1. Obtenha o Código-Fonte

1. Clone este repositório para o seu ambiente de desenvolvimento local:
   ```bash
   git clone git@github.com:douglasbispo/projeto-esp32-mqtt5.git
   ```
2. Navegue para a pasta do projeto
   ```bash
   cd projeto-esp32-mqtt5
   ```

### 2. Abra o projeto no Visual Studio Code

No VS Code, abra a pasta do projeto que você acabou de clonar:

1. Se ainda não fez, configure e Instale a [Extensão ESP-IDF](https://github.com/espressif/vscode-esp-idf-extension).
2. Vá em `File` > `Open Folder...` (ou `Arquivo` > `Abrir Pasta...`).
3. Selecione a pasta `projeto-esp32-mqtt5` (ou o nome que você deu ao projeto clonado) e clique em `Selecionar Pasta`.

### 3. Configure Suas Credenciais Wi-Fi (via `menuconfig`)

As informações da sua rede Wi-Fi são essenciais para que o ESP32 possa se conectar:

1.  **Inicie o Terminal ESP-IDF no VS Code:** use o atalho `Ctrl+Shift+P`  (ou `F1`), digite e selecione a opção **"ESP-IDF: Open ESP-IDF Terminal"**.
2.  Confirme que o terminal está aberto na pasta raiz do seu projeto (ex: `C:\Users\Dbisp\Documents\ESP32_Projetos\projeto-esp32-mqtt5`).
3.  Execute a ferramenta de configuração do ESP-IDF:
    ```Bash
    idf.py menuconfig
    ```
4. Interface do `menuconfig` (a tela que aparece no terminal)
   * Use as setas do teclado para navegar até **`Example Connection Configuration`** e pressione `Enter`.
   * Garanta que a opção `[*] connect using Wifi interface` esteja marcada (com um asterisco `*`).
   * **Desmarque** a opção `[ ] Get ssid and password from stdin` (use a barra de espaço para desmarcar). Isso evita que o ESP32 espere que você digite as credenciais no monitor serial.
   * Selecione **`(myssid) Wifi SSID`**, pressione `Enter`, e digite o **nome exato** da sua rede Wi-Fi. Pressione `Enter` para confirmar.
   * Selecione **`(mypassword) Wifi Password`**, pressione `Enter`, e digite a **senha exata** da sua rede Wi-Fi. Pressione `Enter` para confirmar.
   * Pressione a tecla `Esc` repetidamente (duas vezes ou mais) até aparecer a opção para salvar.
   * Salve as alteração feitas.

### 4. Compile e Faça o Upload (Flash) para o ESP32

Agora é a hora de compilar o código e flashar (gravar) o firmware no seu ESP32:

1.  **Conecte seu ESP32 ao computador** utilizando o cabo USB.
2.  Verifique qual é a **porta COM** atribuída ao seu ESP32. No Gerenciador de Dispositivos do Windows procure em "Portas (COM & LPT)". Anote o número (ex: `COM4`).
3.  Execute o seguinte comando para compilar o projeto e gravar no ESP32, e em seguida, abrir o monitor serial:
    ```Bash
    idf.py -p COMX flash monitor
    ```
    **Substitua `COMX` pelo número da porta COM do seu ESP32** (por exemplo, `idf.py -p COM4 flash monitor`).
4.  Ao término do flashing, o monitor serial será iniciado automaticamente, exibindo os logs de inicialização do ESP32 (conexão Wi-Fi, MQTT, etc.). O ESP32 deverá se conectar e aguardar comandos.

### 5. Teste o Controle do LED com um Cliente MQTT (MQTT Explorer)

Com o ESP32 conectado e aguardando comandos, utilize o MQTT Explorer para enviar as mensagens:

1.  **Instale o MQTT Explorer:** Se ainda não o fez, baixe e instale a aplicação a partir de: [MQTT-EXPLORER](https://mqtt-explorer.com/).
2.  **Abra e Conecte ao Broker:**
    * Inicie o MQTT Explorer.
    * Clique em `+ New Connection`.
    * Preencha os campos:
       * **Protocol**: `mqtt://`.
       * **Host**: `broker.hivemq.com`.
       * **Port**: `1883`.
    * Desabilite o **"Validate certificate"**. Deixe **"Encryption (tls)"** também desabilitado.
    * Deixe os campos "Username" e "Password" em branco.
    * Clique em `save` depois `Connect`.
3.  **Publique os Comandos:**
    * Na interface principal do MQTT Explorer, localize o painel "Publish" no lado direito.
    * No campo **"Topic"**, digite o tópico exato de controle:
        ```
        /ifpe/ads/embarcados/esp32/led
        ```
    * No campo de mensagem (Payload), marque o campo **"raw"**:
        * Para **LIGAR o LED**: Digite `1` e clique no botão **`PUBLISH`**.
        * Para **DESLIGAR o LED**: Digite `0` e clique no botão **`PUBLISH`**.
5.  **Observe o Comportamento:**
    * **No seu ESP32:** verifique se o LED acende (quando digitado `1`) ou apaga (quando digitado `0`).
    * **No Monitor Serial do VS Code:** Você verá as mensagens de log do ESP32 confirmando o recebimento dos dados MQTT e a ação de ligar e desligar o LED (ex: `TOPIC=/ifpe/ads/embarcados/esp32/led`, `DATA=1`, `LED ON`).
  
***

## Desenvolvido por

**Douglas Bispo dos Santos**  
Projeto com fins didáticos.
