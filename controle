/*
  Controle de potência para uma lampada e ventilador com reverso
  
  Usa 3 teclas:
  
  1 - Iluminação - ON/OFF
  2 - Ventilador - 3 VELOCIDADES + DESLIGADO
  3 - Reverso    - 3 VELOCIDADES + DESLIGADO

  Com o ventilador ligado, a tecla Reverso desliga o motor
  Com o reverso ligado, a tecla Ventilador desliga o motor
  
  A mudança de ventilador para reverso é precedida pelo desligamento
  do modo anterior seguido de um delay ajustável via TEMPOMOTOR
  para evitar sobrecarga
  
  A simulação usa uma lâmpada e 2 motores DC para testes 
  mas no circuito prático estes devem ser substituídos por controles
  de potência usando Optoacopladores e TRIACs
  
  Os toques em cada tecla emite um som na saida configurada em TONEOUT
  Observe que devido ao uso da função Tone(); não é recomendável usar os pinos 3,11
  como PWM para saídas em geral. Vide https://www.arduino.cc/reference/en/language/functions/advanced-io/tone/
  
  O status do sistema é reportado na porta serial

  Referencia rápida:
    CONSTANTES
    _VariaveisLocais
    VariaveisGlobais
    FuncoesGerais()
    
  Welington R. Braga
  2018-09-07

 */

#define KEYLUZ     7  //Tecla para iluminação
#define KEYVENT    6  //Tecla para ventilar
#define KEYREVERSE 5  //Tecla para reverso

#define OUTLUZ   13   //Pino saída digital
#define OUTVENT1 10   //Pino saída com PWM
#define OUTVENT2  9   //Pino saída com PWM
#define TONEOUT   3   //Pino saída PWM (tone)

#define TIMETONE   10 //Tempo que o tom de tecla será emitido
#define TONEON   1000 //Frequencia do tom ao ligar
#define TONEOFF   500 //Frequencia do tom ao desligar

#define TEMPOMOTOR 1000 //Tempo de parada do motor em ms
#define TEMPOKEY     30 //Tempo de pressionamento das teclas

int StateLamp = 0;     //Estado da Lampada
int StateVentilador = 0;    //Estado do ventilador
int StateReverse = 0;  //Estado do reverso


/*
 * Função invocada quando o botão de lampada é pressionado
 */
void CheckLamp() {
     
    StateLamp = not StateLamp; //Alterna o estado da lâmpada

    //Emite um tom de acordo com estado definido
    if ( StateLamp ) {
      tone(TONEOUT, TONEON, TIMETONE);
    } else {
      tone(TONEOUT, TONEOFF, TIMETONE);
    } 
    
    Serial.print("Iluminacao ");
    Serial.println(StateLamp);
    
    delay(TEMPOKEY);
}

/*
 * Função invocada quando o botão de ventilador é pressionado
 */
void CheckVentilador() {
       
    //Assegura que o motor no sentido reverso esteja parado
    if (StateReverse > 0 ) {
      StateReverse = 0; 
      analogWrite(OUTVENT2, 0);
      tone(TONEOUT, TONEOFF, TIMETONE);
      Serial.println("Parando Reverso. Aguarde...");
      delay(TEMPOMOTOR);
    }
    //Altera o estado e incrementa a velocidade do ventilador a cada toque no botão
    else if ( StateVentilador > 3 ) {
      StateVentilador = 0;
      tone(TONEOUT,TONEOFF,TIMETONE);
    } else {
      StateVentilador++;
      tone(TONEOUT,TONEON,TIMETONE);
    }
    
    Serial.print("Ventilador ");
    Serial.println(StateVentilador);
    delay(TEMPOKEY);
}

/*
 * Função invocada quando o botão de reverso é pressionado
 */
void CheckReverse() {

    //Assegura que o motor no sentido ventilar esteja parado
    if ( StateVentilador > 0 ) {
      StateVentilador = 0; 
      analogWrite(OUTVENT1, 0);
      tone(TONEOUT, TONEOFF, TIMETONE);
      Serial.println("Parando Ventilador. Aguarde...");
      delay(TEMPOMOTOR);
    }
    //Altera o estado e incrementa a velocidade de reverso a cada toque no botão
    else  if ( StateReverse > 3 ) {
      StateReverse = 0;
      tone(TONEOUT, TONEOFF, TIMETONE);
    } else {
      StateReverse++;
      tone(TONEOUT, TONEON, TIMETONE);
    }

    Serial.print("Reverso ");
    Serial.println(StateReverse);
    delay(TEMPOKEY);

}

void setup() {
  //Inicia monitoramento serial
  Serial.begin(9600);
  
  //Configura pinos das teclas com resistor interno de pull-up
  pinMode(KEYLUZ,     INPUT_PULLUP);
  pinMode(KEYVENT,    INPUT_PULLUP);
  pinMode(KEYREVERSE, INPUT_PULLUP);

  //Configura pinos de saida
  pinMode(OUTLUZ,   OUTPUT);
  pinMode(OUTVENT1, OUTPUT);
  pinMode(OUTVENT2, OUTPUT);

  Serial.println("Ambiente iniciado. Saidas desligadas");

}

void loop() {
  //Leitura do estado dos botões
  //Devido ao uso do PULLUP, default = HIGH
  int _BotaoLamp = digitalRead(KEYLUZ);
  int _BotaoVentilador = digitalRead(KEYVENT);
  int _BotaoReverse = digitalRead(KEYREVERSE);

  //Botao para iluminação pressionado
  if ( _BotaoLamp == LOW ) {
    CheckLamp();
  }
  
  //Botao de ventilador pressionado
  if ( _BotaoVentilador == LOW ) {
    CheckVentilador(); 
  }
  
  //Botao de reverso pressionado
  if ( _BotaoReverse == LOW ) {
    CheckReverse();
  }
  
  // Acende ou apaga a lampada
  if (StateLamp == HIGH) {
    digitalWrite(OUTLUZ, HIGH);
  } else {
    digitalWrite(OUTLUZ, LOW);
  }
 
  //Aciona o campo do ventilador
  //Com 3 velocidades + parado
  switch (StateVentilador) {
    case 0:
      analogWrite(OUTVENT1, 0);
      break;
    case 1:
      analogWrite(OUTVENT1, 64);
      break;
    case 2:
      analogWrite(OUTVENT1, 128);
      break;
    case 3:
      analogWrite(OUTVENT1, 192);
      break;
    case 4:
      analogWrite(OUTVENT1, 255);
      break;
    default:
      analogWrite(OUTVENT1, 0);
      break;    
  }
  
  //Aciona o campo de reverso do ventilador
  //Com 3 velocidades + parado
  switch (StateReverse) {
    case 0:
      analogWrite(OUTVENT2, 0);
      break;
    case 1:
      analogWrite(OUTVENT2, 64);
      break;
    case 2:
      analogWrite(OUTVENT2, 128);
      break;
    case 3:
      analogWrite(OUTVENT2, 192);
      break;
    case 4:
      analogWrite(OUTVENT2, 255);
      break;
    default:
      analogWrite(OUTVENT2, 0);
      break;    
  }
}
