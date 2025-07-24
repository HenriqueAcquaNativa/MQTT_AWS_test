# README Atualizado - Configuração do Código AWS IoT com ESP8266/ESP32 (ARQUIVO FUNCIONAL)

## Introdução
Este guia explica como conectar seu dispositivo ESP8266/ESP32 ao AWS IoT Core usando certificados X.509. O código foi adaptado para melhor desempenho e segurança.

## Configurações Obrigatórias

### 1. Credenciais de WiFi
```cpp
const char* ssid = "SFmall_2G";
const char* password = "@Sfmall2022";
```

### 2. Endpoint AWS IoT
```cpp
const char* AWS_endpoint = "aowe4e5d6ehi2-ats.iot.us-east-2.amazonaws.com";
```

### 3. Arquivos de Certificação
Certifique-se de ter estes arquivos no SPIFFS:
```cpp
/cert.der      // Certificado do dispositivo
/private.der   // Chave privada
/ca.der        // Certificado da CA raiz
```

## Configurações de Memória (Importante!)
O ESP8266 tem limitações críticas de memória:

### Status Atual do Código:
- **RAM**: 36% usado (29.3KB/80KB) - ✅ Seguro
- **IRAM**: 92% usado (60.6KB/64KB) - ⚠️ Crítico!
- **Flash**: 38% usado (399KB/1MB) - ✅ Amplo espaço

### Recomendações de Otimização:
1. **Para IRAM crítica**:
```cpp
void ICACHE_FLASH_ATTR funcaoNaoCritica() {
  // Move código para Flash
}
```

2. **Para economizar RAM**:
```cpp
const char largeText[] PROGMEM = "Texto longo...";
```

3. **Gerenciamento dinâmico**:
```cpp
// Sempre libere memória alocada
delete[] buffer; 
free(ptr);
```

## Implementações Futuras

### 1. Segurança Avançada
- Implementar rotação automática de certificados
- Adicionar verificação de revogação de certificados (CRL/OCSP)
- Implementar criptografia de mensagens no nível de aplicação

### 2. Gerenciamento de Memória
- Sistema de monitoramento em tempo real de heap/stack
- Alocação dinâmica segura com verificações
- Implementar watchdog para reinício seguro

### 3. Resiliência
- Recuperação automática de falhas de conexão
- Armazenamento em buffer de mensagens não enviadas
- Modo de baixo consumo durante falhas

### 4. Monitoramento
- Envio periódico de métricas de desempenho
- Logs remotos com níveis de severidade
- Alertas para condições críticas

## Como Visualizar Mensagens no AWS IoT Core

1. **No AWS Console**:
   - Navegue para IoT Core > Test > MQTT test client
   - Subscreva ao tópico `outTopic`

2. **Enviar comandos**:
   - Publique no tópico `inTopic` com formato JSON:
   ```json
   {"command":"ligar","parametro":"valor"}
   ```

## Dicas de Desenvolvimento

1. **Antes de adicionar novas features**:
   - Verifique o impacto na IRAM usando `ESP.getFreeHeap()`
   - Considere usar o ESP32 para projetos mais complexos

2. **Para depuração**:
   - Ative logs detalhados com:
   ```cpp
   Serial.setDebugOutput(true);
   ```

3. **Em caso de falhas**:
   - Monitore os padrões de reinício (watchdog/reset)
   - Verifique certificados antes da conexão TLS

## Suporte
Para problemas específicos, consulte:
- [Documentação AWS IoT](https://docs.aws.amazon.com/iot/)
- [Referência ESP8266](https://arduino-esp8266.readthedocs.io/)
- [Repositório Original](https://github.com/VeeruSubbuAmi)