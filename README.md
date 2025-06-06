# 💡 Ohmímetro com Raspberry Pi Pico e Matriz de LEDs 💡

## 📝 Descrição Breve

Este projeto utiliza um Raspberry Pi Pico para funcionar como um ohmímetro digital. Ele mede a resistência de um componente desconhecido através de um divisor de tensão com um resistor de referência conhecido (10 kΩ). O valor medido é então exibido em um display OLED SSD1306 (128x64, I2C), juntamente com o valor comercial padrão (série E24) mais próximo e os nomes das cores das faixas correspondentes a esse valor E24. Além disso, uma matriz de LEDs WS2812 5x5 visualiza as três primeiras faixas de cores do resistor E24 correspondente.

## ✨ Funcionalidades Principais ✨

*   **Medição de Resistência:** Utiliza o ADC do Raspberry Pi Pico e um circuito divisor de tensão para determinar a resistência desconhecida.
*   **Cálculo Preciso:** Calcula o valor da resistência com base na leitura do ADC e no valor do resistor de referência.
*   **Aproximação E24:** Encontra o valor de resistor padrão da série E24 mais próximo do valor medido usando uma comparação logarítmica para melhor precisão em diferentes ordens de magnitude.
*   **Display OLED (SSD1306 128x64):**
    *   Exibe o valor bruto lido pelo ADC.
    *   Mostra o valor do resistor fixo de referência (10 kΩ).
    *   Apresenta o valor da resistência medida (R Medido) com o símbolo Ω (Ohm).
    *   Indica o valor da resistência E24 mais próxima (R E24) com o símbolo Ω.
    *   Lista os nomes das 3 primeiras faixas de cores correspondentes ao valor E24.
    *   Mostra a mensagem "Aberto" se a resistência medida for infinita (circuito aberto detectado pelo ADC no valor máximo).
    *   Exibe "Nenhum resistor encontrado" se a resistência medida for superior a 500 kΩ.
*   **Visualização por Matriz de LEDs (WS2812 5x5):**
    *   Representa as 3 faixas de cores do resistor E24 correspondente.
    *   **Mapeamento Específico:**
        *   A **1ª faixa** de cor é exibida na **5ª linha** física da matriz (índice 4).
        *   A **2ª faixa** de cor é exibida na **3ª linha** física da matriz (índice 2).
        *   A **3ª faixa** (multiplicador) é exibida na **1ª linha** física da matriz (índice 0).
        *   As linhas 2 e 4 (índices 1 e 3) permanecem apagadas.
*   **Feedback Visual:** A matriz de LEDs é desligada se nenhum valor E24 for encontrado ou se a resistência medida for considerada muito alta (> 500 kΩ).

## ⚙️ Hardware Necessário ⚙️

*   Raspberry Pi Pico (ou Pico W).
*   Display OLED SSD1306 128x64 com interface I2C.
*   Matriz de LEDs WS2812 5x5 (ou similar compatível, ex: Adafruit NeoPixel).
*   Resistor de Referência: 10 kΩ (recomenda-se um resistor de precisão, 1% ou melhor).
*   Resistores Diversos: Para realizar os testes de medição.
*   Protoboard (Placa de Ensaio).
*   Fios Jumper (Macho-Macho, Macho-Fêmea conforme necessário).
*   Fonte de Alimentação: A porta USB do computador geralmente é suficiente, mas para matrizes de LED com brilho alto, uma fonte externa de 5V pode ser necessária para a matriz.
*   Cabo Micro USB.

## 🔌 Conexões (Wiring) 🔌

### Display OLED (I2C - i2c1)

*   Pico **GP14** (Pin 19 - I2C1 SDA) -> OLED **SDA**
*   Pico **GP15** (Pin 20 - I2C1 SCL) -> OLED **SCL**
*   Pico **3V3(OUT)** (Pin 36) -> OLED **VCC**
*   Pico **GND** (Qualquer pino GND) -> OLED **GND**

### Matriz LED WS2812

*   Pico **GP7** (Pin 10) -> Matriz **DIN** (Data Input)
*   Pico **VBUS** (Pin 40 - 5V do USB) *OU* Fonte Externa **5V** -> Matriz **5V / VCC**
*   Pico **GND** (Qualquer pino GND) -> Matriz **GND**

### Divisor de Tensão (ADC - ADC2)

*   Pico **3V3(OUT)** (Pin 36) -> Uma ponta do **Resistor Conhecido (10kΩ)**
*   Outra ponta do **Resistor Conhecido (10kΩ)** -> Conectar a:
    *   Pico **GP28** (Pin 34 - ADC2)
    *   Uma das pontas do **Resistor Desconhecido** (a ser medido)
*   Outra ponta do **Resistor Desconhecido** -> Conectar ao Pico **GND** (Qualquer pino GND)

### 🖼️ Diagrama de Conexão 🖼️

## 💻 Software e Configuração do Ambiente 🔧

1.  **Instale as Ferramentas:** Certifique-se de ter o ambiente de desenvolvimento para o Raspberry Pi Pico configurado. Isso inclui:
    *   `CMake` (versão 3.13 ou superior)
    *   `ARM GCC Toolchain` (GNU Arm Embedded Toolchain)
    *   `Python 3`
    *   O SDK do Raspberry Pi Pico (`pico-sdk`)
2.  **Configure o SDK:** Siga as instruções oficiais do Raspberry Pi para baixar e configurar o `pico-sdk` e definir as variáveis de ambiente necessárias (ex: `PICO_SDK_PATH`).
3.  **IDE (Opcional):** É recomendado usar o Visual Studio Code com a extensão "Raspberry Pi Pico / RP2040" (ou similar) para facilitar a compilação e o carregamento.
4.  **Clone o Repositório:** Se o código estiver em um repositório Git:
    ```bash
    git clone <URL_DO_REPOSITORIO>
    cd <NOME_DO_DIRETORIO>
    ```
5.  **Submódulos (Pico SDK):** Se o SDK não estiver globalmente configurado, pode ser necessário inicializar submódulos (caso o repositório inclua o SDK):
    ```bash
    git submodule update --init
    ```

## ▶️ Como Compilar e Carregar 🚀

1.  **Crie o diretório de build:**
    ```bash
    mkdir build
    cd build
    ```
2.  **Execute o CMake:**
    ```bash
    cmake ..
    ```
    *(Se o SDK não estiver no caminho padrão, você pode precisar especificar: `cmake .. -DPICO_SDK_PATH=/caminho/para/seu/pico-sdk`)*
3.  **Compile o Projeto:**
    ```bash
    make
    ```
    Isso gerará vários arquivos dentro do diretório `build`, incluindo `Ohmimetro.uf2`.
4.  **Carregue no Pico:**
    *   Desconecte o Pico da alimentação (USB).
    *   Pressione e segure o botão **BOOTSEL** no Pico.
    *   Conecte o Pico ao computador via USB enquanto mantém o BOOTSEL pressionado.
    *   O Pico aparecerá como um dispositivo de armazenamento em massa (como um pendrive) chamado "RPI-RP2".
    *   Solte o botão BOOTSEL.
    *   Copie o arquivo `Ohmimetro.uf2` do diretório `build` para o dispositivo "RPI-RP2".
    *   O Pico reiniciará automaticamente e começará a executar o programa.

## 📁 Estrutura do Código 🏗️

*   `main.c`: Contém a lógica principal do programa, incluindo a inicialização do hardware, leitura do ADC, cálculo da resistência, aproximação E24, determinação das cores, atualização do display OLED e controle da matriz de LEDs.
*   `libs/Display_Bibliotecas/ssd1306.c` / `ssd1306.h`: Biblioteca para controlar o display OLED SSD1306 via I2C, incluindo funções para inicialização, envio de comandos/dados, desenho de pixels, texto e formas.
*   `libs/Display_Bibliotecas/font.h`: Contém os dados bitmap da fonte utilizada no display OLED (caracteres ASCII, números e símbolo Ohm).
*   `libs/Matriz_Bibliotecas/matriz_led.c` / `matriz_led.h`: Biblioteca para controlar a matriz de LEDs WS2812 usando a PIO (Programmable I/O) do Pico. Inclui funções para inicialização, envio de cores e desligamento da matriz.
*   `libs/Matriz_Bibliotecas/generated/ws2812.pio.h`: Arquivo gerado pelo `pioasm` contendo as definições do programa PIO para o controle do protocolo WS2812. (O arquivo fonte é `ws2812.pio`).
*   `CMakeLists.txt`: Arquivo de configuração do CMake que define como o projeto deve ser compilado, quais arquivos incluir e quais bibliotecas do SDK vincular.

## 👉 Como Usar 👉

1.  Compile e carregue o firmware (`Ohmimetro.uf2`) no Raspberry Pi Pico conforme as instruções acima.
2.  Conecte o resistor que você deseja medir nos terminais designados no circuito (entre GP28/ponta do R_conhecido e GND).
3.  Ligue o Pico (conectando-o via USB ou à fonte de alimentação).
4.  Observe o display OLED: ele mostrará os valores medidos e calculados, além dos nomes das cores das faixas E24.
5.  Observe a Matriz de LEDs: ela exibirá as cores das faixas correspondentes ao valor E24. Se nenhum resistor estiver conectado ou se a resistência for muito alta (> 500 kΩ), o display mostrará a mensagem apropriada e a matriz de LEDs ficará apagada.

## 🐛 Debugging 🔍

*   **Saída Serial:** A comunicação serial via USB está habilitada no `CMakeLists.txt` (`pico_enable_stdio_usb(Ohmimetro 1)`). Você pode adicionar chamadas `printf()` no código `main.c` para imprimir valores de variáveis ou mensagens de status.
*   **Monitor Serial:** Use um monitor serial (como o do Arduino IDE, Thonny, `minicom` no Linux, PuTTY no Windows, ou o integrado no VS Code) conectado à porta serial virtual do Pico para visualizar a saída dos `printf`s. A taxa de baud padrão geralmente funciona (ex: 115200).

## 👤 Autor/Contato 👤
 
 Jonas Souza / jonassouza871@hotmail.com