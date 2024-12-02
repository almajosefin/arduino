```
// libraries
#include <WiFi.h>
#include <Arduino_JSON.h>
#include <assert.h>
#include "Arduino_H7_Video.h"
#include "Arduino_GigaDisplayTouch.h"
#include "lvgl.h"

// variables
const char *ssid = "WiFimodem-A07B";
const char *password = "344c18bb54";
char server[] = "www.google.com";
//char server[] = "example.com";
WiFiClient client;    
JSONVar myObject;
Arduino_H7_Video Display(800, 480, GigaDisplayShield);
Arduino_GigaDisplayTouch TouchDetector;

// pointers
lv_obj_t *screen;
lv_obj_t *cont;
lv_obj_t *obj;
lv_obj_t *label;
lv_obj_t *btn;

// button event callback 1
static void btn_event_cb_1(lv_event_t * e) {
  static uint32_t cnt = 1;
  btn = (lv_obj_t *)lv_event_get_target(e);
  cnt++;

  myObject["star"] = (int) 1;
  Serial.println(myObject);

  // server
  Serial.print("Connecting to server...");
  if (client.connect(server, 80)) {
    Serial.println("Server connected.");

    client.println("GET /search?q=arduino HTTP/1.1");
    //client.println("GET /index.html HTTP/1.1");
    client.print("Host: ");
    client.println(server);
    client.println("Connection: close");
    client.println();

    // client.println("POST /api/ReviewController HTTP/1.1");
    // client.print("Host: ");
    // client.println(server);
    // client.println("User-Agent: Arduino/1.0");
    // client.println("Connection: close");
    // client.print("Content-Length: ");
    // client.println(myObject.length());
    // client.println();
    // client.println(myObject);

  }
}

// button event callback 2
static void btn_event_cb_2(lv_event_t * e) {
  static uint32_t cnt = 1;
  btn = (lv_obj_t *)lv_event_get_target(e);
  cnt++;

  myObject["star"] = (int) 2;
  Serial.println(myObject);

// button event callback 3
static void btn_event_cb_3(lv_event_t * e) {
  static uint32_t cnt = 1;
  btn = (lv_obj_t *)lv_event_get_target(e);
  cnt++;

  myObject["star"] = (int) 3;
  Serial.println(myObject);
}

  /*
  if button one is clicked
  button one color equals yellow

  if button two is clicked
  buttons one & two colors equal yellow

  if button three is clicked
  buttons one, two & three colors equal yellow

  visual confirmation
  change button colors back
  */

void setup() {
  Serial.begin(9600);
  WiFi.begin(ssid, password);
  Display.begin();
  TouchDetector.begin();

  //client.begin();
  //client.addHeader("Content-Type: application/json");

  // wifi
  Serial.print("Connecting to WiFi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected.");

  // screen
  screen = lv_obj_create(lv_scr_act()); // screen pointer
  lv_obj_set_size(screen, Display.width(), Display.height()); // screen size

  // grid
  static lv_coord_t col_dsc[] = {740, LV_GRID_TEMPLATE_LAST};
  static lv_coord_t row_dsc[] = {140, 280, LV_GRID_TEMPLATE_LAST};
  cont = lv_obj_create(lv_scr_act()); // grid pointer
  lv_obj_set_grid_dsc_array(cont, col_dsc, row_dsc); // grid array
  lv_obj_set_size(cont, Display.width(), Display.height()); // grid size
  lv_obj_center(cont); // grid placement

  // grid text
  obj = lv_obj_create(cont);
  lv_obj_set_grid_cell(obj, LV_GRID_ALIGN_STRETCH, 0, 1, LV_GRID_ALIGN_STRETCH, 0, 1);
  lv_obj_set_flex_flow(obj, LV_FLEX_FLOW_COLUMN);

  // text
  label = lv_label_create(obj); // text pointer
  lv_label_set_text(label, "Hvordan var dit besoeg?"); // text content
  lv_obj_center(label); // text placement

  // grid buttons
  obj = lv_obj_create(cont);
  lv_obj_set_grid_cell(obj, LV_GRID_ALIGN_STRETCH, 0, 1, LV_GRID_ALIGN_STRETCH, 1, 1);
  lv_obj_set_flex_flow(obj, LV_FLEX_FLOW_COLUMN);

  // button 1
  btn = lv_btn_create(obj); // button pointer
  lv_obj_set_size(btn, 100, 100); // button size
  lv_obj_center(btn); // button placement
  lv_obj_add_event_cb(btn, btn_event_cb_1, LV_EVENT_CLICKED, NULL); // button event callback
  // button shape
  // button color

  // button 2
  btn = lv_btn_create(obj); // button pointer
  lv_obj_set_size(btn, 100, 100); // button size
  lv_obj_center(btn); // button placement
  lv_obj_add_event_cb(btn, btn_event_cb_2, LV_EVENT_CLICKED, NULL); // button event callback
  // button shape
  // button color

  // button 3
  btn = lv_btn_create(obj); // button pointer
  lv_obj_set_size(btn, 100, 100); // button size
  lv_obj_center(btn); // button placement
  lv_obj_add_event_cb(btn, btn_event_cb_3, LV_EVENT_CLICKED, NULL); // button event callback
  // button shape
  // button color
}

void loop() {
  // wifi
  if (WiFi.status() != WL_CONNECTED) {
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
      delay(500);
      Serial.print(".");
    }
    Serial.println("WiFi reconnected.");
  }

  // server
  while (client.available()) {
    char c = client.read();
    Serial.write(c);
  }

  lv_timer_handler();
}
```
