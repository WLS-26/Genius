// ********************************************************* 
//                        JOGO GENIUS
//            MINI PROJETO: MICROCONTROLADORES
// ***********************************************************


#include <Arduino.h>
#include <Bounce2.h>
#include <Tone.h>

const int LedPins[] = {2, 3, 4, 5}; // Pinos dos leds
const int ButtonsPins[] = {6, 7, 8, 9}; //Pinos dos botões coorelacionado aos leds
const int BuzzerPin[] = {10}; // Pino do Buzzer
const int StartButton[] = {11}; // Pino do botão de iniciar o jogo

int Sequence[10]; // Sequência dos 10 níveis para o jogo
int UserSequence[10]; // Máximo de sequência que pode ser inserida pelo usuário
int Level= 0; // level atual
int PressionarBotao; // Ao pressionar botão
