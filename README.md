# HomeEco
温度、湿度、照度を計測し表示する装置


#include <M5Unified.h>

void setup() {
  M5.begin();
  M5.Power.begin();

  // 背景（薄茶色）
  uint16_t bgColor = M5.Lcd.color565(210,180,140);
  M5.Lcd.fillScreen(bgColor);

  // ラベル（固定表示・少し大きめ）
  M5.Lcd.setTextColor(BLACK, bgColor);
  M5.Lcd.setTextSize(3);
  M5.Lcd.setCursor(20, 20);  M5.Lcd.printf("Temp:");
  M5.Lcd.setCursor(20, 70);  M5.Lcd.printf("Hum :");
  M5.Lcd.setCursor(20, 120); M5.Lcd.printf("Lux :");
  M5.Lcd.setTextSize(2);
  M5.Lcd.setCursor(20, 180); M5.Lcd.printf("Fan :");
  M5.Lcd.setCursor(20, 210); M5.Lcd.printf("LED :");
}

// ===== 値を表示（部分更新でチラつきなし） =====
void drawValues(float temp, float hum, int lux, bool fanOn, bool ledOn) {
  uint16_t bgColor = M5.Lcd.color565(210,180,140);

  // === 温度 ===
  uint16_t tempColor = (temp >= 30) ? RED : (temp <= 20) ? CYAN : GREEN;
  M5.Lcd.setTextSize(4);
  M5.Lcd.setTextColor(tempColor, bgColor);
  M5.Lcd.fillRect(150, 40, 170, 40, bgColor);
  M5.Lcd.setCursor(150, 20);
  M5.Lcd.printf("%.1f C", temp);

  // === 湿度 ===
  uint16_t humColor = (hum >= 80) ? PURPLE : (hum <= 60) ? BLUE : GREEN;
  M5.Lcd.setTextColor(humColor, bgColor);
  M5.Lcd.fillRect(150, 90, 170, 40, bgColor);
  M5.Lcd.setCursor(150, 70);
  M5.Lcd.printf("%.1f %%", hum);

  // === 照度 ===
  uint16_t luxColor = (lux >= 300) ? WHITE : (lux <= 30) ? BLACK : YELLOW;
  M5.Lcd.setTextColor(luxColor, bgColor);
  M5.Lcd.fillRect(150, 140, 170, 40, bgColor);
  M5.Lcd.setCursor(150, 120);
  M5.Lcd.printf("%d lx", lux);

  // === デバイス状態 ===
  M5.Lcd.setTextSize(3);
  M5.Lcd.setTextColor(BLACK, bgColor);
  M5.Lcd.fillRect(150, 200, 100, 30, bgColor);
  M5.Lcd.setCursor(150, 180);
  M5.Lcd.printf("%s", fanOn ? "ON" : "OFF");

  M5.Lcd.fillRect(150, 230, 100, 30, bgColor);
  M5.Lcd.setCursor(150, 210);
  M5.Lcd.printf("%s", ledOn ? "ON" : "OFF");
}

void loop() {
  // 仮のセンサーデータ（実センサーに置き換え可）
  float temp = 10.4;
  float hum  = 60.2;
  int   lux  = 10;
  bool  fanOn = true;
  bool  ledOn = false;

  // 前回値との比較
  static float lastTemp = -999;
  static float lastHum  = -999;
  static int   lastLux  = -999;
  static bool  lastFan  = false;
  static bool  lastLed  = false;

  if (temp != lastTemp || hum != lastHum || lux != lastLux ||
      fanOn != lastFan || ledOn != lastLed) {

    drawValues(temp, hum, lux, fanOn, ledOn);

    lastTemp = temp;
    lastHum  = hum;
    lastLux  = lux;
    lastFan  = fanOn;
    lastLed  = ledOn;
  }

  delay(1000);
}