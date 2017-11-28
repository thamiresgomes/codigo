 




Projeto Elevador com Arduino 






Alunos: 
   Felipe Martins Dadalto
               Thamires Gomes Alves 
                                                            Glauco Sá Othon Sidou
                                                             Raul Arruda Cipriano 

Disciplina: 
Princípio de Controle 
Horário: NCD35
Professor: 
Afonso Henriques Fontes Neto Segundo


Fortaleza-CE
2017
Introdução

   No projeto proposto, demonstraremos a montagem e funcionamento de um elevador de pequeno porte (apenas para uma pequena amostragem), constituído por 4 andares onde, em seu princípio de funcionamento, partirá do controle de um arduino programado que irá fazer a leitura de uma ação   ( que neste caso, será o acionamento de um dos botões por um agente externo - para indicar qual andar seguir ) , e por fim, fazer com que o elevador se movimente para o andar desejado.
   Temos como atuador um servo motor cc, em que irá movimentar de forma mecânica o elevador, os 4 botões do tipo normalmente fechado em que ficará exposto externamente em cada andar , e o arduino em que irá se comportar como controlador do projeto ( a partir da programação ). 
Atuador: Servo Motor
Controlador: Arduino
Sensor: Encoder



Lista de materiais utilizados :

•	1 Servo Motor CC;
•	1 Arduino; 
•	4 Resistores de 1K cada; 
•	1 Encoder; 
•	4 botões normalmente fechado; 
•	4 transistores;
•	Madeira;
•	Alicate;
•	/Chave de fenda;
•	Cola quente;
•	Ferro de solda;
•	Fio;
•	Cabo de alimentação arduino/pc;
Embasamento Teórico/Prático

Diagrama/Fluxograma funcional do projeto:

 
















Esquemático do Projeto:

 

Fonte : https://www.tinkercad.com














Diagrama em blocos: Com a realimentação.

 
Explicação : Neste diagrama (malha fechada), iniciamos o funcionamento do elevador com o acionamento de um dos botões como indicado acima na entrada ( input ). Após acionado, o arduíno lê o comando, verifica qual andar prosseguir, e logo após, aciona também o servomotor em que será controlado ( ligar e desligar ) pela programação (output). Importante destacar a ação do encoder, que atua como realimentação do sistema, fazendo com que também controle a velocidade e a posição exata da programação.











Diagrama em blocos: Sem a realimentação .

 

Explicação : Neste diagrama (malha aberta), notamos que o funcionamento é o mesmo como o explicado anteriormente , porém, não temos o encoder como realimentação , no que afetará no descontrole da velocidade do servomotor, em que atuará na sua descida/subida em função do tempo e, seu torque poderá variar em função do peso (carga) no elevador em que está acoplado ao servomotor. 









IMPLEMENTAÇAO

Para a implementação, foram elaborados dois códigos-fonte, um para o funcionamento com a realimentação em função da rotação, em malha fechada, onde o encoder será o responsável pela realimentação (H(s)), e outro para o funcionamento apenas em função do tempo (delay), sem nenhuma realimentação do circuito.
 Haverá a descrição do código, para a explicação de cada item especifico, exemplificando o seu funcionamento.
De modo geral, o controlador (Arduino) recebe o sinal do botão quando pressionado, e gera o respectivo sinal para o servo motor, seja este sinal trabalhado pelo Tempo ou pelo Encoder.

 
Código-fonte (Malha Aberta - TEMPO):
#include <Servo.h>

int botao1 = 2;				   // botao 1o andar ligado na entrada 2.
int botao2 = 3;				   // botao 2o andar ligado na entrada 3.
int botao3 = 4;				   // botao 3o andar ligado na entrada 4.
int botao4 = 5;				   // botao 4o andar ligado na entrada 5.
int andaratual = 1;
int andardesej = 0;
int destino = 0;
int contador = 0;
Servo servo;				   // iniciando o comando do servo e dando um novo nome.


void setup() {
  // put your setup code here, to run once:
  Serial.begin (9600);
  Serial.println ("Cuida negada");
  pinMode(botao1, INPUT);
  pinMode(botao2, INPUT);
  pinMode(botao3, INPUT);
  pinMode(botao4, INPUT);
  pinMode(encoder, INPUT);
  servo.attach (9);   			   // servo ligado na porta 9.
  servo.write (90); 				   // angulo necessário para deixar o servo parado.
}

void loop() {
  int b1 = digitalRead (botao1);     
  int b2 = digitalRead (botao2);
  int b3 = digitalRead (botao3);
  int b4 = digitalRead (botao4);

  if (b1 == LOW) {                                                 // Este botao eh normalmente fechado (1, HIGH), logo, ao 
    andardesej = 1;                                                  // ser acionado, o sinal dele vai para 0 (LOW).
    Serial.println ("1o Andar");		  // Acionamento do 1o andar.
    destino = andaratual - andardesej;                  // O destino será a diferença entre o andar atual e o
  }           					  // andar desejado. Caso negativo, o servo girara no
  else {					  // sentido anti-horário, caso positivo, girara no sentido horário.
     					  // O mesmo processo ocorre nos demais andares, variando
    if (b2 == LOW) { 			  // apenas a entrada do respectivo botao (b1, b2, b3 b4).
  
    andardesej = 2;
    Serial.println ("2o Andar");
    destino = andaratual - andardesej;
                   }
  
  else 
  {
    
  if (b3 == LOW) {
    andardesej = 3;
    Serial.println ("3o Andar");
    destino = andaratual - andardesej;
                 }

                
  else 
  {
  
  if (b4 == LOW) {
    andardesej = 4;
    Serial.println ("4o Andar");
    destino = andaratual - andardesej;
                 }
  else 
  {
    andardesej = 0;
  }

  }
  
  }
  
  }
  
  switch (destino) {				// Caso o destino seja 0, nao havera mudanca de andar, logo o
    case 0:  				// servo ficara parado (angulo 90º), e para nao haver nenhuma
    {               				// falha no envio deste sinal repetidamente enquanto ocorre o
      servo.write (90);                 		// loop, desabilitamos o servo (detach) enquanto o destino seja
      servo.detach(); 			// igual a 0.
    }
    break;
    
    case -1:
    {
      servo.attach (9);
      servo.write (130);
      Serial.println ("Subindo 1 andar");
      delay(3700);
      servo.write (90);
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;
    
    case -2: 					// Neste caso, o destino esta igual a -2, logo o motor ira 
    { 						// girar no respectivo sentido. Pelo destino ser 2, o 
      servo.attach (9);				// tempo será equivalente ao de subir dois andares. O 
      servo.write (130);				// tempo será definido através do delay. Ao chegar no
      Serial.println ("Subindo 2 andares"); 		// andar desejado, o servo para, voltando para o ângulo 
      delay(7450); 					// 90º. O andar atual passa a ser o andar desejado.
      servo.write (90);				// Após esta mudança, o destino e o andar desejado
      destino = 0;					// voltam para o valor 0, para sair do Switch e do If.
      andaratual = andardesej;			// O mesmo ocorre para os outros destinos, mudando
      andardesej = 0; 				// apenas o sentido e o tempo.
    }
    break;

    case -3:
    {
      servo.attach (9);
      servo.write (130);
      Serial.println ("Subindo 3 andares");
      delay(11200);
      servo.write (90);
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;

    case 1:
    {
      servo.attach (9);
      servo.write (70);
      Serial.println ("Descendo 1 andar");
      delay(3400);
      servo.write (90);
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;

    case 2:
    {
      servo.attach (9);
      servo.write (70);
      Serial.println ("Descendo 2 andares");
      delay(6800);
      servo.write (90);
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;
    
    case 3:
    {
      servo.attach (9);
      servo.write (70);
      Serial.println ("Descendo 3 andares");
      delay(10300);
      servo.write (90);
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;
  }  
}









 
Código-fonte (Malha Fechada - ENCODER):
#include <Servo.h>

int botao1 = 2; 				   // botao 1o andar ligado na entrada 2.
int botao2 = 3; 				   // botao 2o andar ligado na entrada 3.
int botao3 = 4; 				   // botao 3o andar ligado na entrada 4.
int botao4 = 5; 				   // botao 4o andar ligado na entrada 5.
int encoder = 8; 				   // encoder ligado na entrada 8.
int andaratual = 1;
int andardesej = 0;
int destino = 0;
int flag = 0;
int contador = 0;
Servo servo; 				   // iniciando o comando do servo e dando um novo nome.


void setup() {
  // put your setup code here, to run once:
  Serial.begin (9600);
  Serial.println ("Cuida negada");
  pinMode(botao1, INPUT);
  pinMode(botao2, INPUT);
  pinMode(botao3, INPUT);
  pinMode(botao4, INPUT);
  pinMode(encoder, INPUT);
  
  }

void loop() {
  // put your main code here, to run repeatedly:
  int b1 = digitalRead (botao1);
  int b2 = digitalRead (botao2);
  int b3 = digitalRead (botao3);
  int b4 = digitalRead (botao4);
  servo.attach(9);   			   // servo ligado na porta 9.
  servo.write (90); 				   // angulo necessário para deixar o servo parado.

  if (b1 == LOW) {                                                 // Este botao eh normalmente fechado (1, HIGH), logo, ao 
    andardesej = 1;                                                  // ser acionado, o sinal dele vai para 0 (LOW).
    Serial.println ("1o Andar");		  // Acionamento do 1o andar.
    destino = andaratual - andardesej;                  // O destino será a diferença entre o andar atual e o
  }           					  // andar desejado. Caso negativo, o servo girara no
  else {					  // sentido anti-horário, caso positivo, girara no sentido horário.
     					  // O mesmo processo ocorre nos demais andares, variando
    if (b2 == LOW) { 			  // apenas a entrada do respectivo botao (b1, b2, b3 b4).
  
    andardesej = 2;
    Serial.println ("2o Andar");
    destino = andaratual - andardesej;
                   }
  
  else 
  {
    
  if (b3 == LOW) {
    andardesej = 3;
    Serial.println ("3o Andar");
    destino = andaratual - andardesej;
                 }

                
  else 
  {
  
  if (b4 == LOW) {
    andardesej = 4;
    Serial.println ("4o Andar");
    destino = andaratual - andardesej;
                 }
  else 
  {
    andardesej = 0;
  }

  }
  
  }
  
  }
  
  
  switch (destino) {				// Caso o destino seja 0, nao havera mudanca de andar, logo o
    case 0:  				// servo ficara parado (angulo 90º), e para nao haver nenhuma
    {               				// falha no envio deste sinal repetidamente enquanto ocorre o
      servo.write (90);                 		// loop, desabilitamos o servo (detach) enquanto o destino seja
      servo.detach(); 			// igual a 0.
      
    }
    break;
    
    case -1:
    {
      servo.attach(9);			// Neste caso, o destino está igual a -1, logo o motor ira
      servo.write (115);			// girar no respectivo sentido para chegar ao destino. O encoder 
      Serial.println ("Subindo 1 andar");	// trabalha com pulsos,
      contador = 0;				// para completar uma volta completa, são necessários um deter-
      while (contador<46){			// minado números de pulsos. O motor ira girar o suficiente para
        if (digitalRead(encoder)== HIGH) {	// chegar ao seu destino especificado, assim, não sofrendo modi-
          if (flag == 0) {			// cação do seu trajeto devido a peso (TORQUE) ou Velocidade.
            contador ++;
            Serial.print ("contador    :");
            Serial.println (contador);
            flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
    }
    break;
    
    case -2:				 
    {
      servo.attach(9);
      servo.write (115);
      Serial.println ("Subindo 2 andares");
      contador = 0;
      while (contador<83){
        if (digitalRead(encoder)== HIGH) {
          if (flag == 0) {
            contador ++;
            Serial.print ("contador    :");
            Serial.println (contador);
            flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
    }
    break;

    case -3:
    {
      servo.attach(9);
      servo.write (115);
      Serial.println ("Subindo 3 andares");
      contador = 0;
      while (contador<126){
        if (digitalRead(encoder)== HIGH) {
          if (flag == 0) {
           contador ++;
           Serial.print ("contador    :");
           Serial.println (contador);
           flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
    }
    break;

    case 1:
    {
      servo.attach(9);
      servo.write (70);
      Serial.println ("Descendo 1 andar");
      contador = 0;
      while (contador<42){
        if (digitalRead(encoder)== HIGH) {
          if (flag == 0) {
            contador ++;
            Serial.print ("contador    :");
            Serial.println (contador);
            flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
    }
    break;

    case 2:
    {
      servo.attach(9);
      servo.write (70);
      Serial.println ("Descendo 2 andares");
      contador = 0;
      while (contador<75){
        if (digitalRead(encoder)== HIGH) {
          if (flag == 0) {
            contador ++;
            Serial.print ("contador    :");
            Serial.println (contador);
            flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
    }
    break;
    
    case 3:
    {
      servo.attach(9);
      servo.write (70);
      Serial.println ("Descendo 3 andares");
      contador = 0;
      while (contador<112){
        if (digitalRead(encoder)== HIGH) {
          if (flag == 0) {
            contador ++;
            Serial.print ("contador    :");
            Serial.println (contador);
            flag = 1;
          }
        } 
        else {
          flag = 0;
        }
      }
      servo.write (90);
      contador = 0;
      flag = 0;
      destino = 0;
      andaratual = andardesej;
      andardesej = 0;
    }
    break;
  }  
}












































Conclusão

O desenvolvimento do projeto obteve progresso durante seu funcionamento em primeira instância (diagrama em malha aberta, Arduino respondendo em tempo através do delay). Porém, em testes realizados em diferentes situações , foi verificado que de acordo com a carga introduzida no sistema ( elevador ) , seu bom funcionamento foi comprometido. Tal comprometimento deu-se através do sistema de comando obedecer impulsos de tempo desprezando a carga depositada no elevador em que, consequentemente afetaria a rotação no eixo do servo motor, ou seja, o tempo necessário para sair do andar inicial ao andar desejado sofreria alteração, ocasionando a paralização do elevador no andar/local indesejado.
Em estudos, constatamos que tal problema poderia ser solucionado com o acréscimo de um sensor ( encoder ), tornando assim o projeto em diagrama malha fechada, onde o servo motor responderia a partir de impulsos elétricos do sensor. Na implementação, obtivemos novamente um segundo problema, em que, a quantidade de impulsos necessários para mover o elevador do estado inicial ao desejado não estava sendo constante ( fornecendo assim impulsos um pouco alternado ), logo, a paralização do elevador poderia parar novamente em um andar/local indesejado. 
Para modo de projeto , finalizamos assim a partir do funcionamento pelo tempo, em que, para diminuir a porcentagem de erro de paralização do servo motor ( em que iria ocasionar parar o elevador no andar desejado ) , diminuímos a velocidade do motor na programação do Arduino, em que afetaria no seu torque, logo, tivemos um torque maior para se caso fosse alocado um carga no elevador , não afetasse no seu mau funcionamento.    
