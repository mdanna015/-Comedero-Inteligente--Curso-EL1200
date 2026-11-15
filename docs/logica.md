# Explicación de la Lógica del Comedero Inteligente

## Objetivo del sistema
El comedero dispensa alimento **solo una vez** cada vez que el gato se acerca, evitando que el servo siga girando si el gato se queda frente al sensor.

---

## Componentes y pines utilizados

| Componente       | Pin ESP32 | Función                          |
|------------------|-----------|----------------------------------|
| Sensor PIR       | GPIO 2    | Detecta la presencia del gato    |
| Servomotor       | GPIO 13   | Abre y cierra la tapa            |
| LED              | GPIO 5    | Indica que se está dispensando  |

---

## Variables importantes

- `anguloDispensa = 140` → Posición en la que cae el alimento  
- `anguloCerrado = 40` → Posición de reposo (tapa cerrada)  
- `dispensado` → Bandera lógica (true/false) que evita servir comida varias veces seguidas

---

## Funcionamiento paso a paso

### 1. Inicialización (`setup`)
- Se configura el sensor PIR como entrada.
- Se configura el LED como salida.
- Se asocia el servomotor al pin 13 y se coloca en posición cerrada (40°).
- El LED se apaga.
- El sistema queda en modo **espera**.

### 2. Ciclo principal (`loop`)

1. Se lee el estado del sensor PIR.
2. **Si se detecta movimiento** (`hayGato == HIGH`):
   - Se verifica si ya se dispensó comida (`dispensado == false`).
   - Si **aún no se ha dispensado**:
     - El servo se mueve a 140° (abre la tapa).
     - Se enciende el LED.
     - Se espera 1.5 segundos para que caiga el alimento.
     - El servo regresa a 40° (cierra la tapa).
     - Se apaga el LED.
     - Se cambia la bandera `dispensado = true` (ya no se puede volver a servir).
     - Se espera 10 segundos para evitar re-disparos.
3. **Si no se detecta movimiento**:
   - Si la bandera estaba en `true`, se reinicia a `false`.
   - El sistema queda listo para la próxima detección.

---

## Resumen de la lógica de control

La variable `dispensado` es la clave del sistema:

- `false` → El comedero está listo para servir.
- `true` → Ya se sirvió comida y no se volverá a servir hasta que el gato se aleje.

Esto evita que el servo gire continuamente mientras el gato permanece frente al sensor.
