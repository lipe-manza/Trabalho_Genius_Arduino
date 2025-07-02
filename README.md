# Genius no Arduino
*Segundo trabalho da disciplina Eletronica para Computação (SSC0180)*

# Sobre o projeto
Para o segundo trabalho, fizemos um jogo da memória estilo o jogo Genius. Nele, o jogador precisa apertar os leds na ordem em que aparecem, precisando ganhar 13 rodadas para ganhar o jogo.

# Funcionamento
Com relação ao funcionamento do projeto, temos quatro leds, com suas pernas positivas ligadas nos resistores que se conectam com os pinos do Arduíno. As pernas negativas dos leds se conectam na parte negativa da protoboard, que se conecta com o GND do arduíno. O mesmo acontece com os botões, com a diferença que suas partes positivas se conectam diretamente no Arduíno, não necessitando de um resistor. O buzzer, por outro lado, tem ambas as suas pernas conectadas direto aos pinos do Arduíno. Depois de conectar todos os componentes na placa e conectá-la ao computador rodando código, os leds começam a piscar até que algum botão seja pressionado. Ao pressionar algum botão, o jogo tem início. 

# Componentes
Os componentes utilizados foram:
•	4 Resistores de 300 ohms
•	4 Chaves Momentâneas (Push Button)
•	1 Buzzer
•	4 Leds de Cores Diferentes
•	Jumpers (Macho/Macho)
Chegando a um total de aproximadamente 40 reais.

# Fotos e vídeo
<img src="URL_da_Imagem" alt="Foto arduíno">

# Código do arduíno
```
#define CHOICE_OFF      0 //Usado para controlar LEDs

#define CHOICE_NONE     0 //Usado para verificar botões

#define CHOICE_RED  (1 << 0)

#define CHOICE_GREEN    (1 << 1)

#define CHOICE_BLUE (1 << 2)

#define CHOICE_YELLOW   (1 << 3)



#define LED_RED     2

#define LED_GREEN   4

#define LED_BLUE    6

#define LED_YELLOW  8



// Definições dos pinos de botão

#define BUTTON_RED    3

#define BUTTON_GREEN  5

#define BUTTON_BLUE   7

#define BUTTON_YELLOW 9



// Definições dos pinos do Buzzer

#define BUZZER1  10

#define BUZZER2  11



// Parâmetros do jogo

#define ROUNDS_TO_WIN      13 // Número de rodadas para vencer o Jogo.

#define ENTRY_TIME_LIMIT   3000 // Tempo para pressionar um botão antes que o jogo acabe. 3000ms = 3 seg



#define MODE_MEMORY  0



// Variáveis de estado do jogo

byte gameMode = MODE_MEMORY; //Por padrão, vamos jogar o jogo da memória

byte gameBoard[32]; //Salva a combinação de botões à medida que avançamos

byte gameRound = 0; //Conta o número de rodadas de sucesso que o jogador fez



void setup()

{

  // Configuração dos pinos de entradas e saídas



  // Ativa pull ups em entradas

  pinMode(BUTTON_RED, INPUT_PULLUP);

  pinMode(BUTTON_GREEN, INPUT_PULLUP);

  pinMode(BUTTON_BLUE, INPUT_PULLUP);

  pinMode(BUTTON_YELLOW, INPUT_PULLUP);



  pinMode(LED_RED, OUTPUT);

  pinMode(LED_GREEN, OUTPUT);

  pinMode(LED_BLUE, OUTPUT);

  pinMode(LED_YELLOW, OUTPUT);



  pinMode(BUZZER1, OUTPUT);

  pinMode(BUZZER2, OUTPUT);



  // Verificação de modo

  gameMode = MODE_MEMORY; // Por padrão, vamos jogar o jogo da memória

  play_winner(); // Após a conclusão da configuração, diga olá ao mundo

}



void loop()

{

  attractMode(); // Pisca luzes enquanto aguarda o usuário apertar um botão



  // Indique o início do jogo

  setLEDs(CHOICE_RED | CHOICE_GREEN | CHOICE_BLUE | CHOICE_YELLOW); // Ativar todos os LEDs

  delay(1000);

  setLEDs(CHOICE_OFF); // Desligue os LEDs

  delay(250);



  if (gameMode == MODE_MEMORY)

  {

    // Play no jogo de memória e recebe com resultado

    if (play_memory() == true) 

      play_winner(); // Ganhou, toca som vitória

    else 

      play_loser(); // Perdeu, toca som derrota

  }

}



//-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

//As seguintes funções estão relacionadas apenas ao jogo



// Jogue o jogo da memória

// Retorna 0 se o jogador perde ou 1 se o jogador ganhar

boolean play_memory(void)

{

  randomSeed(millis()); // Gerador aleatório



  gameRound = 0; // Redefinir o jogo para o começo



  while (gameRound < ROUNDS_TO_WIN) 

  {

    add_to_moves(); // Adicione um botão aos movimentos atuais e reproduza-os



    playMoves(); // Jogue de volta o tabuleiro do jogo atual



    // Em seguida, solicite ao jogador que repita a sequência.

    for (byte currentMove = 0 ; currentMove < gameRound ; currentMove++)

    {

      byte choice = wait_for_button(); // Veja o botão que o usuário pressiona



      if (choice == 0) return false; // Se a espera expirar, o jogador perde



      if (choice != gameBoard[currentMove]) return false; // Se a escolha estiver incorreta, o jogador perde

    }



    delay(1000); // O jogador estava correto, espera antes de jogar

  }



  return true; // O jogador cumpriu todas as rodadas para ganhar!

}

// Reproduz o conteúdo atual dos movimentos do jogo

void playMoves(void)

{

  for (byte currentMove = 0 ; currentMove < gameRound ; currentMove++) 

  {

    toner(gameBoard[currentMove], 150);



    // Aguarde algum tempo entre a reprodução do botão

    delay(150); // 150 funciona bem. 75 fica rápido.

  }

}



// Adiciona um novo botão aleatório à sequência do jogo

void add_to_moves(void)

{

  byte newButton = random(0, 4); //min (incluido), max (excluido)



  // Temos que converter esse número, 0 até 3, para CHOICEs

  if(newButton == 0) newButton = CHOICE_RED;

  else if(newButton == 1) newButton = CHOICE_GREEN;

  else if(newButton == 2) newButton = CHOICE_BLUE;

  else if(newButton == 3) newButton = CHOICE_YELLOW;



  gameBoard[gameRound++] = newButton; // Adicionar este novo botão ao array do jogo

}



//-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

// As seguintes funções controlam o hardware



// ilumina um determinado LEDs

// Passar em um byte composto de CHOICE_RED, CHOICE_YELLOW, etc

void setLEDs(byte leds)

{

  if ((leds & CHOICE_RED) != 0)

    digitalWrite(LED_RED, HIGH);

  else

    digitalWrite(LED_RED, LOW);



  if ((leds & CHOICE_GREEN) != 0)

    digitalWrite(LED_GREEN, HIGH);

  else

    digitalWrite(LED_GREEN, LOW);



  if ((leds & CHOICE_BLUE) != 0)

    digitalWrite(LED_BLUE, HIGH);

  else

    digitalWrite(LED_BLUE, LOW);



  if ((leds & CHOICE_YELLOW) != 0)

    digitalWrite(LED_YELLOW, HIGH);

  else

    digitalWrite(LED_YELLOW, LOW);

}



// Aguarde até que um botão seja pressionado.

// Retorna uma das cores do LED (LED_RED, etc.) se for bem-sucedida, 0 se expirar

byte wait_for_button(void)

{

  long startTime = millis(); // Lembre-se da hora em que começamos o loop



  while ( (millis() - startTime) < ENTRY_TIME_LIMIT) // Faz um loop até que passe muito tempo

  {

    byte button = checkButton();



    if (button != CHOICE_NONE)

    { 

      toner(button, 150); // Reproduzir o botão que o usuário acabou de pressionar



      while(checkButton() != CHOICE_NONE) ;  // Agora vamos esperar que o usuário libere o botão



      delay(10); // Isso ajuda com toques duplos acidentais



      return button;

    }



  }



  return CHOICE_NONE; // Se chegarmos aqui, expiramos!

}



// Retorna um bit '1' na posição correspondente a CHOICE_RED, CHOICE_GREEN, etc.

byte checkButton(void)

{

  if (digitalRead(BUTTON_RED) == 0) return(CHOICE_RED); 

  else if (digitalRead(BUTTON_GREEN) == 0) return(CHOICE_GREEN); 

  else if (digitalRead(BUTTON_BLUE) == 0) return(CHOICE_BLUE); 

  else if (digitalRead(BUTTON_YELLOW) == 0) return(CHOICE_YELLOW);



  return(CHOICE_NONE); // Se nenhum botão for pressionado, não retorne nenhum

}



// Acenda um LED e toque o tom

// Vermelho, superior esquerdo: 440Hz - 2.272ms - 1.136ms de pulso

// Verde, superior direito: 880Hz - 1.136ms - pulso de 0.568ms

// Azul, inferior esquerdo: pulso de 587.33Hz - 1.702ms - 0.851ms

// Amarelo, inferior direito: 784Hz - 1,276ms - pulso de 0,638ms

void toner(byte which, int buzz_length_ms)

{

  setLEDs(which); // Ligue um dado LED



  // Reproduz o som associado ao LED fornecido

  switch(which) 

  {

  case CHOICE_RED:

    buzz_sound(buzz_length_ms, 1136); 

    break;

  case CHOICE_GREEN:

    buzz_sound(buzz_length_ms, 568); 

    break;

  case CHOICE_BLUE:

    buzz_sound(buzz_length_ms, 851); 

    break;

  case CHOICE_YELLOW:

    buzz_sound(buzz_length_ms, 638); 

    break;

  }



  setLEDs(CHOICE_OFF); // Desligue todos os LEDs

}



// Alterna o buzzer a cada buzz_delay_us, por uma duração de buzz_length_ms.

void buzz_sound(int buzz_length_ms, int buzz_delay_us)

{

  // Converter tempo total de reprodução de milissegundos para microssegundos

  long buzz_length_us = buzz_length_ms * (long)1000;



  // Faz um loop até que o tempo restante de reprodução seja menor que um único buzz_delay_us

  while (buzz_length_us > (buzz_delay_us * 2))

  {

    buzz_length_us -= buzz_delay_us * 2; // Diminui o tempo de jogo restante



    // Alterna a campainha em várias velocidades

    digitalWrite(BUZZER1, LOW);

    digitalWrite(BUZZER2, HIGH);

    delayMicroseconds(buzz_delay_us);



    digitalWrite(BUZZER1, HIGH);

    digitalWrite(BUZZER2, LOW);

    delayMicroseconds(buzz_delay_us);

  }

}



// Reproduzir o som e as luzes do vencedor

void play_winner(void)

{

  setLEDs(CHOICE_GREEN | CHOICE_BLUE);

  winner_sound();

  setLEDs(CHOICE_RED | CHOICE_YELLOW);

  winner_sound();

  setLEDs(CHOICE_GREEN | CHOICE_BLUE);

  winner_sound();

  setLEDs(CHOICE_RED | CHOICE_YELLOW);

  winner_sound();

}



// Som vencedor

// Este é apenas um som (chato) único que criamos, não há mágica nisso

void winner_sound(void)

{

  // Alterna a campainha em várias velocidades

  for (byte x = 250 ; x > 70 ; x--)

  {

    for (byte y = 0 ; y < 3 ; y++)

    {

      digitalWrite(BUZZER2, HIGH);

      digitalWrite(BUZZER1, LOW);

      delayMicroseconds(x);



      digitalWrite(BUZZER2, LOW);

      digitalWrite(BUZZER1, HIGH);

      delayMicroseconds(x);

    }

  }

}



// Jogue o som perdedor / luzes

void play_loser(void)

{

  setLEDs(CHOICE_RED | CHOICE_GREEN);

  buzz_sound(255, 1500);



  setLEDs(CHOICE_BLUE | CHOICE_YELLOW);

  buzz_sound(255, 1500);



  setLEDs(CHOICE_RED | CHOICE_GREEN);

  buzz_sound(255, 1500);



  setLEDs(CHOICE_BLUE | CHOICE_YELLOW);

  buzz_sound(255, 1500);

}



// Mostra uma tela de "modo de atração" enquanto aguarda o usuário pressionar o botão.

void attractMode(void)

{

  while(1) 

  {

    setLEDs(CHOICE_RED);

    delay(100);

    if (checkButton() != CHOICE_NONE) return;



    setLEDs(CHOICE_BLUE);

    delay(100);

    if (checkButton() != CHOICE_NONE) return;



    setLEDs(CHOICE_GREEN);

    delay(100);

    if (checkButton() != CHOICE_NONE) return;



    setLEDs(CHOICE_YELLOW);

    delay(100);

    if (checkButton() != CHOICE_NONE) return;

  }

}
```
# Integrantes
- Rafael Said Jannini - 16898162
- 

