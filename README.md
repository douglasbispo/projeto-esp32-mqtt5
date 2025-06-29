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

## Hardware e Software usados

Para replicar este projeto, você precisará dos seguintes itens:

### Hardware

* **Placa de Desenvolvimento ESP32:** placa de desenvolvimento.
* **Cabo USB-C:** Um cabo de dados USB funcional para conectar o ESP32 ao seu computador.
* **Conectividade de Rede:** Acesso a uma rede Wi-Fi de 2.4GHz.

### Software

* **ESP-IDF (Espressif IoT Development Framework):** Instale e configure em seu sistema. Para instruções de instalação, consulte o [Guia de Primeiros Passos do ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html).
* **Visual Studio Code (VS Code):** Editor de código, Instale como uma extensão: [Extensão ESP-IDF](https://github.com/espressif/vscode-esp-idf-extension).
* **Cliente MQTT:** Aplicação para publicar mensagens MQTT. Sugiro o [MQTT Explorer](https://mqtt-explorer.com/) por sua interface intuitiva.
* **Broker MQTT:** Um servidor MQTT acessível (por exemplo, o broker público `broker.hivemq.com` na porta 1883 para testes, ou seu próprio broker local).

***

## Como executar o projeto

Clone este repositório:

```
git clone git@github.com:douglasbispo/projeto-esp32-mqtt5.git
```

### 2. Abrir o projeto no Visual Studio Code

No VS Code, abra a pasta do projeto que você acabou de clonar:

* Vá em `File` > `Open Folder...` (ou `Arquivo` > `Abrir Pasta...`).

### 3. Configure seu Wi-Fi (via `menuconfig`)

Configurar sua rede Wi-Fi para que o ESP32 possa se conectar:

1.  **Inicie o Terminal ESP-IDF no VS Code:** use o atalho `Ctrl+Shift+P`, digite e selecione a opção **"ESP-IDF: Open ESP-IDF Terminal"**.
2.  Confirme que o terminal está aberto na pasta raiz do seu projeto.
3.  Execute a ferramenta de configuração do ESP-IDF:
    ```
    idf.py menuconfig
    ```
4.  Dentro da interface do `menuconfig` (a tela que aparece no terminal):
    * Use as setas do teclado para navegar até **`Example Connection Configuration`** e pressione `Enter`.
    * **Desmarque** a opção `[ ] Get ssid and password from stdin` (use a barra de espaço para desmarcar).
    * Selecione **`(myssid) Wifi SSID`**, pressione `Enter`, e digite o **nome exato** da sua rede Wi-Fi. Pressione `Enter` para confirmar.
    * Selecione **`(mypassword) Wifi Password`**, pressione `Enter`, e digite a **senha exata** da sua rede Wi-Fi. Pressione `Enter` para confirmar.
    * Pressione a tecla `Esc` duas vezes para voltar ao menu principal `(Top)`.
    * Selecione **`< Save >`** e pressione `Enter` para confirmar a alteração das configurações.
    * Selecione **`< Exit >`** e pressione `Enter` para sair do `menuconfig`.

### 4. Compile e Faça o flash no ESP32

Compile o código e flash(grave) o firmware no seu ESP32:

1.  **Conecte seu ESP32 ao computador** utilizando o cabo USB.
2.  Verifique qual é a **porta COM** do seu ESP32. No Gerenciador de Dispositivos do Windows procure em "Portas (COM & LPT)". Anote o número (ex: `COM4`).
3.  No **Terminal ESP-IDF** no VS Code, se o monitor serial estiver ativo, pressione `Ctrl + c` depois `Ctrl + x` para fechá-lo.
4.  Execute o seguinte comando para compilar o projeto e gravar no ESP32:
    ```
    idf.py -p COM4 flash monitor
    ```
    **Substitua `COM4` pelo número da porta COM do seu ESP32**.
5.  Ao termino do flashing, o ESP32 deverá se conectar e aguardar comandos.

### 5. Teste o Controle do LED com um Cliente MQTT (MQTT Explorer)

Com o ESP32 conectado e aguardando comandos, utilize o MQTT Explorer para enviar as mensagens:

1.  **Instale o MQTT Explorer:** baixe e instale: [MQTT-EXPLORER](https://mqtt-explorer.com/).
2.  **Abra e Conecte ao Broker:**
    * Clique em `+ New Connection`.
    * Preencha os campos: **Protocol**: `mqtt://`, **Host**: `broker.hivemq.com`, **Port**: `1883`.
    * Desabilite o **"Validate certificate"**. Deixe **"Encryption (tls)"** também desabilitado.
    * Deixe os campos "Username" e "Password" em branco.
    * Clique em `save` depois `Connect`.
3.  **Interface principal do MQTT Explore:**
    * No campo **"Topic"** no lado direito, digite o tópico:
        ```
        /ifpe/ads/embarcados/esp32/led
        ```
    * No campo de mensagem, abaixo de tiopic, marque o campo "raw":
        * Para LIGAR o LED: Digite `1` e clique no botão **`PUBLISH`**.
        * Para DESLIGAR o LED: Digite `0` e clique no botão **`PUBLISH`**.
4.  **Observe o Comportamento:**
    * **No seu ESP32:** verifique se o LED acende (quando digitado `1`) ou apaga (quando digitado `0`).
    * **No Monitor Serial do VS Code:** Você verá as mensagens de log do ESP32 confirmando o recebimento dos dados MQTT e a ação de ligar e desligar o LED.
