# Arduino-FEC
Forward Error Correction library with Reed-Solomon<br>
Based on mersinvald's code:<br>
https://github.com/mersinvald/Reed-Solomon

# Install
Donwload, and import into Arduino IDE

# Exmaple
```
#include <RS-FEC.h>

char message[] = "Some very important message";
const int msglen = 60;  const uint8_t ECC_LENGTH = 10;  //Max message lenght, and "guardian bytes", Max corrected bytes ECC_LENGTH/2
char message_frame[msglen]; // The message size would be different, so need a container
char repaired[msglen];    char encoded[msglen + ECC_LENGTH];

RS::ReedSolomon<msglen, ECC_LENGTH> rs;


void setup() {
  Serial.begin(115200);      while (!Serial);             // initialize serial
 
  memset(message_frame, 0, sizeof(message_frame));        // Clear the array
  for(uint i = 0; i <= msglen; i++) {    message_frame[i] = message[i];     } // Fill with the message
 
  rs.Encode(message_frame, encoded); 
 
  Serial.print("Original: "); Serial.println(message_frame);
  Serial.print("Encoded:  ");        for(uint i = 0; i < sizeof(encoded); i++) {    Serial.print(encoded[i]);    }    Serial.println("");
  for(uint i = 20; i < 25; i++) {        encoded[i] = '-';    } //Let's steal some byte from 20 to 25.
  Serial.print("Corrupted: ");      for(uint i = 0; i < sizeof(encoded); i++) {     Serial.print(encoded[i]);    }    Serial.println("");
  rs.Decode(encoded, repaired);
  String result;    memcmp(message_frame, repaired, msglen) == 0 ? result="SUCCESS" : result="FAILURE"; //Compare the arrays
  Serial.print("Result: ");   Serial.println(result);
  Serial.print("Repaired: "); Serial.println(repaired);
    
}

void loop() { }
```

The Output:
```
Original: Some very important message
Encoded:  Some very important message?ÃŠÃ¼?
Corrupted: Some very important -----ge?ÃŠÃ¼?
Result: SUCCESS
Repaired: Some very important message
```


