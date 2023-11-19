//*******************************************
//*    Mini-Projeto Microcontroladores      *
//*              Jogo Genius                *
//*******************************************

//variaveis que vai ser utilizado no código
int sequencia[10] = {};
int botoes[4] = { 6, 7, 8, 9 };
int BotaoIniciar = 11;
int leds[4] = { 2, 3, 4, 5 };
int tons[4] = { 349, 330, 294, 262 };
int level = 0;
int etapa = 0;
int BotaoPressionado = 0;
bool GameOver = false;
bool JogoIniciado = false;

void setup() {
  // Essa primeira parte do código é o setup para ligar os componentes que estão no protoboard com o arduino
  //Os pinos que estão conectados os leds
  pinMode(2, OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
  //O pino que está conectado o Buzzer
  pinMode(10, OUTPUT);
  //O pino que está conectado o botão Start
  pinMode(BotaoIniciar, INPUT_PULLUP);
  //Os pinos que etão conectados os botões para interligar ao led
  pinMode(6, INPUT_PULLUP);
  pinMode(7, INPUT_PULLUP);
  pinMode(8, INPUT_PULLUP);
  pinMode(9, INPUT_PULLUP);
  Serial.begin(9600);
  randomSeed(analogRead(A0));
}
void loop() {
  //Essa parte do código do jogo é responsável por iniciar o jogo genius
  if (digitalRead(BotaoIniciar) == LOW && !JogoIniciado) {
    Serial.println("Desencadeando o poder espiritual, o jogo se inicia");
    delay(1000); // Aguarda um tempo para evitar detecção múltipla do botão
    JogoIniciado = true;
  }

  if (JogoIniciado) {
    //Essa parte do código do jogo é responsável por reiniciar o jogo a caso o jogador erre a sequência
    if (GameOver == true) {
      if (digitalRead(BotaoIniciar) == LOW) {
        Serial.println("Renascendo como um herói, o jogo recomeça!");
        sequencia[10] = {};
        level = 0;
        etapa = 0;
        GameOver = false;
        JogoIniciado = false; // Reinicia o jogo
        Serial.println("Retornando ao início, o jogo recomeça!");
        delay(1000); // Aguarda um tempo para evitar detecção múltipla do botão
      }
    } else {
      //Essa parte do código é responsável pela lógica principal. Ficando a questão da próxima rodada, o aguardo do jogador para gerar uma nova sequencia
      proximaRodada();
      reproduzirSequencia();
      aguardarJogada();
      delay(1000);

      // Verifica se todos os níveis foram concluídos
      if (level == 10) {
        Serial.println("Ultrapassei meus limites. Essa é a essência de um verdadeiro herói!");

        // Aciona todos os LEDs por 2 segundos para dizer que finalizou o jogo
        for (int i = 0; i < 4; i++) {
        digitalWrite(leds[i], HIGH);
        }

        // Emite um som de vitória
        tone(10, 500);
        delay(2000);
        noTone(10);

        // Desliga todos os LEDs
        for (int i = 0; i < 4; i++) {
          digitalWrite(leds[i], LOW);
        }

        // Reinicia o jogo para um novo conjunto de 10 níveis
        level = 0;
        etapa = 0;
        GameOver = false;
        Serial.println("Iniciando novo conjunto de níveis...");
        delay(2000); // Aguarda um tempo antes de iniciar o próximo conjunto de níveis
      }
    }
  }
}
void proximaRodada() {
  //Essa parte do código tem a função de gerar a próxima jogada na sequência, adicionando lá. 
  //Essa parte do código também tem a função de atualizar o nível e exibir no serial
  int sorteio = random(4);
  sequencia[level] = sorteio;
  level = level + 1;
  Serial.print("Seguindo em frente - Próxima jogada: ");
  for (int i = 0; i < level; i++) {
    Serial.print(sequencia[i]);
  }
  Serial.println();
}

void reproduzirSequencia() {
  //Essa parte do código reproduz a sequência gerada até o nível atual
  //Essa parte também irá acender o led e ativará o buzzer em milissegundos 
  for (int i = 0; i < level; i++) {
    tone(10, tons[sequencia[i]]);
    digitalWrite(leds[sequencia[i]], HIGH);
    delay(500);
    noTone(10);
    digitalWrite(leds[sequencia[i]], LOW);
    delay(100);
  }
}
void aguardarJogada() {
  //Essa parte do código irá aguardar a resposta do jogador a cada elemento da sequência
  for (int i = 0; i < level; i++) {
    bool jogada_efetuada = false;
    while (!jogada_efetuada) {
      for (int i = 0; i <= 3; i++) {
        if (digitalRead(botoes[i]) == 0) {
          BotaoPressionado = i;
          tone(10, tons[i]);
          digitalWrite(leds[i], HIGH);
          delay(300);
          digitalWrite(leds[i], LOW);
          noTone(10);
          jogada_efetuada = true;
        }
      }
    }
    Serial.print("Seleção: ");
    Serial.println(BotaoPressionado);

    if (sequencia[etapa] != BotaoPressionado) {
      //Essa parte do código se o jogador errar a sequência de elementos, o jogo irá indicar o termino do jogo
      Serial.println("Finish him! Seu fracasso é fatal. Fim de jogo." );
      for (int i = 0; i <= 3; i++) {
        tone(10, 70);
        digitalWrite(leds[i], HIGH);
        delay(100);
        digitalWrite(leds[i], LOW);
        noTone(10);
      }
      GameOver = true;
      return;
    }

    etapa = etapa + 1;

    if (etapa == level) {
      //Essa parte do código quando o jogador aceertar, ele irá dizer que completou a rodada com sucesso
      Serial.println("Missão cumprida! Preparando-se para o próximo desafio.");
      etapa = 0;
      delay(1000);
    }
  }
}
