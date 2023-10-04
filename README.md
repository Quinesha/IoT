# IoT
Documentation for IoT class

# Assignment
Stel de LEDstrip kleur via een colorpicker in via Adafruit IO. adafruit IO . 
Dit is een andere library dan Fastled, er bestaan meerdere libraries (software) voor dezelfde hardware. 
Soms is de ene meer geschikt voor wat jij wil, soms de andere.

## Step 1 
Collect the required components.
In this case we'll require

- 1x Arduino Board
- 1x LEDstip
- Arduino IDE
- Adafruit library

## Step 2
Install the right library in the Arduino IDE

Select the Library manager of the Arduino IDE search for "Adafruit IO Arduino"

<img width="198" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/5c335852-e8dc-4bea-8652-00b51778b4b6">

Scroll trhough the serach results until you find: "Adafruit AW9523 by Adafruit"
When you've succesfully found the right library, click on install -> install all.

Now wait until it's been installed.

## Step 3
Next, connect the cables from the LEDstrip to the Arduino board.

- connect a wire from 5V to 3V3
- connect the middle wire (Din) to D5
- connect GND wire to GND

Now you're all set :)

## Step 4
Write your code and if it doesn't go well, ask ChatGPT for help.

other resources:
https://www.youtube.com/watch?v=abL9dq_Pzm4
https://randomnerdtutorials.com/esp32-esp8266-rgb-led-strip-web-server/

''' 
// Load Wi-Fi library
#include <ESP8266WiFi.h>

// Replace with your network credentials
const char* ssid     = "Mahlerstraat";
const char* password = "ehZdNxwe7isxnawp";

// Set web server port number to 80
WiFiServer server(80);

// Decode HTTP GET value
String redString = "0";
String greenString = "0";
String blueString = "0";
int pos1 = 0;
int pos2 = 0;
int pos3 = 0;
int pos4 = 0;

// Variable to store the HTTP req  uest
String header;

// Red, green, and blue pins for PWM control
const int redPin = 13;     // 13 corresponds to GPIO13
const int greenPin = 12;   // 12 corresponds to GPIO12
const int bluePin = 14;    // 14 corresponds to GPIO14

// Setting PWM bit resolution
const int resolution = 256;

// Current time
unsigned long currentTime = millis();
// Previous time
unsigned long previousTime = 0; 
// Define timeout time in milliseconds (example: 2000ms = 2s)
const long timeoutTime = 2000;

void setup() {
  Serial.begin(115200);
  
  // configure LED PWM resolution/range and set pins to LOW
  analogWriteRange(resolution);
  analogWrite(redPin, 0);
  analogWrite(greenPin, 0);
  analogWrite(bluePin, 0);
  
  // Connect to Wi-Fi network with SSID and password
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  // Print local IP address and start web server
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  server.begin();
}

void loop(){
  WiFiClient client = server.available();   // Listen for incoming clients

  if (client) {                             // If a new client connects,
    currentTime = millis();
    previousTime = currentTime;
    Serial.println("New Client.");          // print a message out in the serial port
    String currentLine = "";                // make a String to hold incoming data from the client
    while (client.connected() && currentTime - previousTime <= timeoutTime) {            // loop while the client's connected
      currentTime = millis();
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c);                    // print it out the serial monitor
        header += c;
        if (c == '\n') {                    // if the byte is a newline character
          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println("Connection: close");
            client.println();
                   
            // Display the HTML web page
            client.println("<!DOCTYPE html><html>");
            client.println("<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">");
            client.println("<link rel=\"icon\" href=\"data:,\">");
            client.println("<link rel=\"stylesheet\" href=\"https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css\">");
            client.println("<script src=\"https://cdnjs.cloudflare.com/ajax/libs/jscolor/2.0.4/jscolor.min.js\"></script>");
            client.println("</head><body><div class=\"container\"><div class=\"row\"><h1>ESP Color Picker</h1></div>");
            client.println("<a class=\"btn btn-primary btn-lg\" href=\"#\" id=\"change_color\" role=\"button\">Change Color</a> ");
            client.println("<input class=\"jscolor {onFineChange:'update(this)'}\" id=\"rgb\"></div>");
            client.println("<script>function update(picker) {document.getElementById('rgb').innerHTML = Math.round(picker.rgb[0]) + ', ' +  Math.round(picker.rgb[1]) + ', ' + Math.round(picker.rgb[2]);");
            client.println("document.getElementById(\"change_color\").href=\"?r\" + Math.round(picker.rgb[0]) + \"g\" +  Math.round(picker.rgb[1]) + \"b\" + Math.round(picker.rgb[2]) + \"&\";}</script></body></html>");
            // The HTTP response ends with another blank line
            client.println();

            // Request sample: /?r201g32b255&
            // Red = 201 | Green = 32 | Blue = 255
            if(header.indexOf("GET /?r") >= 0) {
              pos1 = header.indexOf('r');
              pos2 = header.indexOf('g');
              pos3 = header.indexOf('b');
              pos4 = header.indexOf('&');
              redString = header.substring(pos1+1, pos2);
              greenString = header.substring(pos2+1, pos3);
              blueString = header.substring(pos3+1, pos4);
              /*Serial.println(redString.toInt());
              Serial.println(greenString.toInt());
              Serial.println(blueString.toInt());*/
              analogWrite(redPin, redString.toInt());
              analogWrite(greenPin, greenString.toInt());
              analogWrite(bluePin, blueString.toInt());
            }
            // Break out of the while loop
            break;
          } else { // if you got a newline, then clear currentLine
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }
      }
    }
    // Clear the header variable
    header = "";
    // Close the connection
    client.stop();
    Serial.println("Client disconnected.");
    Serial.println("");
  }
}

'''

## Step 5
Upload your code by clicking on the right facing arrow.

<img width="30" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/4bb33fb2-552e-4fbd-a822-4400ce27919a">

Wait until the code has uploaded anddd watch the magic happen!


## My mistakes
None, everything went exactly the way I needed it to orr did it?

<img width="542" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/be10aba3-7d48-494e-aea0-10f64c3416f2">
<img width="516" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/da948c71-dcb6-452e-8ec4-83783a92e1b6">
<img width="566" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/624ed1cf-3d74-408c-9e01-4a3844843f69">
<img width="924" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/55d85921-4ffd-4efc-9a52-370d7133b685">
<img width="753" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/8fe0413b-cb50-4900-80fc-57a9c1e33798">







## Sources
- My own imagination
- ChatGPT
- https://docs.google.com/document/d/1l_9xpU-n7Rhxj-L3wI72Kvc3GQ9q6tFl955J2NTYF9k/edit
- https://docs.google.com/document/d/14jhaxaJUhuu0p6_u_oNj8_50Y6PAmtrvcePtKc0HDGs/edit
