#include <EEPROM.h>

int n = ???;                  //how meny memory cells you would like to use?
unsigned char value[44];       //put 44 here according to atack value create n memory cells (you need to change "n" so it will be the same as int n)
int i = 0;                    //data pointer
char command;                 //storage the command from the eeprom
int instruction_pointer = 0;  //instruction pointer
boolean x = false;
int y = 0;

void setup()
{
  do                //put the value zero in all of the memory cells
  {
  value[i] = 0;
  i++;
  }
  while(i < 44);
 
  i = 0;
 
  Serial.begin(9600);
 
  Serial.print("hello");
  Serial.print("\n");

//-----------put your BF code into the arduino eeprom memory-------------------  
  EEPROM.write(0, '+');         //write brainfuck code to the arduinos eeprom
  EEPROM.write(1, '[');         //write brainfuck code to the arduinos eeprom
  EEPROM.write(2, ',');         //write brainfuck code to the arduinos eeprom
  EEPROM.write(3, '.');         //write brainfuck code to the arduinos eeprom
  EEPROM.write(4, ']');         //write brainfuck code to the arduinos eeprom
//-----------this is a "cat" example-------------------------------------------  
 
  Serial.print("the code:");
  Serial.print("\n");  
 
  command = EEPROM.read(i);
  while(command == '>' || command == '<' || command == '+' || command == '-' || command == ',' || command == '.' || command == '[' || command == ']')
  {
  command = EEPROM.read(i);
  Serial.print(command);
  i++;
  }
 
  i = 0;
 
  Serial.print("\n");
  Serial.print("start...");
  Serial.print("\n");
  Serial.print("user interface:");
  Serial.print("\n");
  Serial.print(">>");
  Serial.print("\n");
 
}


void loop()
{
 
  command = EEPROM.read(instruction_pointer);        //read the command
 
   switch (command)                                  //cheack what command is it
   {
     
     case '>' :                    
      ++i;
      break;
     
//-------------------------------------------------------------      
   
     case '<':
      --i;
      break;
     
//-------------------------------------------------------------    
     
     case '+':
      ++value[i];
      break;  
     
//-------------------------------------------------------------      
   
     case '-':
      --value[i];
      break;  
     
//-------------------------------------------------------------            
   
     case '.':
     if(value[i] == 10)
      {
       Serial.println(">>");        //new line (10 at the ascii chart)
      }
     Serial.println(value[i]);
     break;  
     
//-------------------------------------------------------------  
 
     case ',':
     while(x == false)
     {
     if (Serial.available() > 0)  
       {
        value[i] = Serial.read();
        x = true;
        }  
      }
     x = false;
     break;  
     
//------------------------------------------------------------      
 
     case '[':       if(value[i] == 0)
       {
         y = 1;
          while(y != 0)
          {
            ++instruction_pointer;
            command = EEPROM.read(instruction_pointer);
             if(command == '[')
              {
               y++;
              }
             else if(command == ']')
              {
               y--;
              }
           }
       }
   
     break;
     
//-------------------------------------------------------------      
   
     case ']':
      if(value[i] != 0)
       {
         y = 1;
          while(y != 0)
          {
            --instruction_pointer;
            command = EEPROM.read(instruction_pointer);
             if(command == ']')
              {
               y++;
              }
             else if(command == '[')
              {
               y--;
              }
           }
       }
     
     break;
     
//-------------------------------------------------------------        
 
 default:
     Serial.print("error:end of the progrem");    //didnt got a command
     return;

   }
   
  ++instruction_pointer;                         //next command
 
}

