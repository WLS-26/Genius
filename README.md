#include <Arduino.h>
#include <Bounce2.h>
#include <Tone.h>

const int LedPins[] = {2, 3, 4, 5};         // Pinos dos leds
const int ButtonsPins[] = {6, 7, 8, 9};      // Pinos dos botões correlacionados aos leds
const int BuzzerPin[] = {10};                // Pino do Buzzer
const int StartButton[] = {11};              // Pino do botão de iniciar o jogo
const int FreqLed[] = {262, 294, 330, 349};

int Sequence[10];       // Sequência dos 10 níveis para o jogo
int UserSequence[10];   // Máximo de sequência que pode ser inserida pelo usuário
int Level = 0;          // Nível atual
int PressionarBotao;    // Ao pressionar botão

void setup() {
  // Configurar os pinos dos LEDs como saída
  for (int i = 0; i < 4; i++) {
    pinMode(LedPins[i], OUTPUT);
  }

  // Configurar os pinos dos botões como entrada com pull-up
  for (int i = 0; i < 4; i++) {
    pinMode(ButtonsPins[i], INPUT_PULLUP);
  }

  // Configurar o pino do botão de início como entrada com pull-up
  pinMode(StartButton[0], INPUT_PULLUP);

  // Configurar o pino do buzzer como saída
  pinMode(BuzzerPin[0], OUTPUT);
}

void loop() {
  IniciarJogo();
  IniciarGame();
}

// Função para acender um LED com uma frequência específica
void AcenderLED(int IndiceLed, int duracao = 500) {
  // Desligar todos os LEDs
  for (int i = 0; i < 4; i++) {
    digitalWrite(LedPins[i], LOW);
  }

  // Ligar o LED específico
  digitalWrite(LedPins[IndiceLed], HIGH);

  // Tocar a frequência associada ao LED
  tone(BuzzerPin[0], FreqLed[IndiceLed]);
  delay(400); // tempo associado ao som

  // Desligar o buzzer
  noTone(BuzzerPin[0]);
}

// Função para gerar uma sequência aleatória
void gerarSequencia() {
  for (int i = 0; i < 10; i++) {
    Sequence[i] = random(0, 4); // Gera um número aleatório entre 0 e 3
  }
}

// Função para iniciar o jogo
void IniciarJogo() {
  if (digitalRead(StartButton[0]) == LOW) {
    // Botão "Start" pressionado
    gerarSequencia();

    // Acender todos os LEDs e tocar um som por 2 segundos
    for (int i = 0; i < 4; i++) {
      AcenderLED(i, 300);
    }
    delay(2000);

    // Desligar todos os LEDs e o buzzer
    for (int i = 0; i < 4; i++) {
      digitalWrite(LedPins[i], LOW);
    }
    noTone(BuzzerPin[0]);

    // Iniciar o jogo
    Level = 0;
  }
}

// Função para jogar o jogo
void IniciarGame() {
  if (Level < 10) {
    // Aguardar o jogador pressionar um botão
    PressionarBotao = waitForButtonPress();

    // Comparar a entrada do jogador com a sequência
    if (PressionarBotao == Sequence[Level]) {
      // Se o botão estiver correto, acende o LED correspondente
      AcenderLED(PressionarBotao);
      Level++;
    } else {
      // Se o botão estiver errado, toca um som de erro
      tone(BuzzerPin[0], 1000, 500); // Toca um som de erro por 500ms
      delay(1000); // Aguarda antes de reiniciar o jogo
      Level = 0; // Reinicia o jogo
    }
  } else {
    // O jogador completou todos os níveis, toca um som de vitória
    for (int i = 0; i < 5; i++) {
      for (int j = 0; j < 4; j++) {
        AcenderLED(j, 200);
      }
    }
    delay(2000); // Aguarda antes de reiniciar o jogo
    Level = 0; // Reinicia o jogo
  }
}

// Função para esperar o jogador pressionar um botão
int waitForButtonPress() {
  int BotaoPressionado = -1;
  while (BotaoPressionado == -1) {
    for (int i = 0; i < 4; i++) {
      if (digitalRead(ButtonsPins[i]) == LOW) {
        BotaoPressionado = i;
        delay(100); // Debounce
        while (digitalRead(ButtonsPins[i]) == LOW) {
          // Aguardar até que o botão seja liberado
        }
      }
    }
  }
  return BotaoPressionado;
}
