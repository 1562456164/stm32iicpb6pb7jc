# stm32iicpb6pb7jc
stm32iicpb6pb7jc



#include <Wire.h>

// 标注扫描的I2C接口（I2C1对应PB6=SCL，PB7=SDA）
const char* i2cInfo = "I2C1(SCL=PB6,SDA=PB7)";

void setup() {
  Serial.begin(115200);
  while (!Serial) { delay(100); }  // 等待串口连接

  Wire.begin();  // 初始化I2C1（旧核心默认绑定PB6/PB7）

  Serial.println("===== STM32F103C8T6 I2C扫描 =====");
  Serial.println("扫描接口：" + String(i2cInfo));
  Serial.println("格式:7位地址 / 8位写地址\n");
}

void loop() {
  Serial.println("----- 开始扫描 " + String(i2cInfo) + " -----");
  int deviceCount = scanI2CDevices();  // 调用修正后的函数名

  if (deviceCount == 0) {
    Serial.println("未找到I2C设备");
  } else {
    Serial.print("共找到 ");
    Serial.print(deviceCount);
    Serial.println(" 个设备");
  }

  Serial.println("\n2秒后重新扫描...\n");
  delay(2000);
}

// 修正函数名（去掉空格，正确命名为scanI2CDevices）
int scanI2CDevices() {
  byte error;
  uint8_t sevenBitAddr;
  int count = 0;

  for (sevenBitAddr = 1; sevenBitAddr < 127; sevenBitAddr++) {
    Wire.beginTransmission(sevenBitAddr);
    error = Wire.endTransmission();

    if (error == 0) {
      uint8_t eightBitWriteAddr = sevenBitAddr << 1;  // 计算8位写地址

      Serial.print("设备地址:7位=0x");
      if (sevenBitAddr < 0x10) Serial.print("0");
      Serial.print(sevenBitAddr, HEX);

      Serial.print(" | 8位写地址=0x");
      if (eightBitWriteAddr < 0x10) Serial.print("0");
      Serial.println(eightBitWriteAddr, HEX);

      count++;
    }
  }
  return count;
}
