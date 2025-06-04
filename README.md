# API del Juego del Gato (Tic-Tac-Toe)

Esta es una API REST completa para jugar al "gato" (tic-tac-toe) implementada en Python con FastAPI.

## Características

- ✅ Juego para 1 jugador (vs CPU) o 2 jugadores
- ✅ Múltiples partidas simultáneas
- ✅ Almacenamiento de partidas y jugadas
- ✅ Reinicio automático en caso de empate
- ✅ Estadísticas completas
- ✅ Validaciones de jugadas y turnos
- ✅ CPU con estrategia básica

## Requisitos

- Python 3.8+
- pip

## Instalación

1. Clona el repositorio:
```bash
git clone <url-del-repositorio>
cd juego-gato-api
```

2. Instala las dependencias:
```bash
pip install -r requirements.txt
```

3. Ejecuta la aplicación:
```bash
python main.py
```

O usando uvicorn directamente:
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

La API estará disponible en `http://localhost:8000`

## Documentación de la API

Una vez que la aplicación esté ejecutándose, puedes acceder a:
- **Documentación interactiva (Swagger)**: `http://localhost:8000/docs`
- **Documentación alternativa (ReDoc)**: `http://localhost:8000/redoc`

## Endpoints

### 1. Iniciar nueva partida
**POST** `/game/new`

Crea una nueva partida del juego.

**Body:**
```json
{
  "mode": "1"  // "1" para 1 jugador vs CPU, "2" para 2 jugadores
}
```

**Respuesta:**
```json
{
  "game_code": "A1B2C3D4",
  "mode": "1",
  "starting_player": "X",
  "current_player": "X",
  "board": [["","",""],["","",""],["","",""]],
  "message": "Nueva partida iniciada. Comienza el jugador X"
}
```

### 2. Realizar jugada
**POST** `/game/move`

Realiza una jugada en una partida existente.

**Body:**
```json
{
  "game_code": "A1B2C3D4",
  "player": "X",
  "row": 1,
  "col": 1
}
```

**Respuesta:**
```json
{
  "game_code": "A1B2C3D4",
  "board": [["","",""],["","X",""],["","",""]],
  "move": {"player": "X", "row": 1, "col": 1},
  "status": "in_progress",
  "current_player": "O",
  "winner": null
}
```

### 3. Ver partidas
**GET** `/games?game_code=A1B2C3D4` (opcional)

Ver todas las partidas o una específica por código.

**Respuesta (partida específica):**
```json
{
  "game": {
    "id": "uuid-game-id",
    "code": "A1B2C3D4",
    "mode": "1",
    "start_time": "2024-01-15T10:30:00",
    "end_time": "2024-01-15T10:35:00",
    "move_count": 5,
    "winner": "X",
    "status": "finished",
    "board": [["X","O","X"],["O","X","O"],["","",""]],
    "moves": [
      {
        "player": "X",
        "row": 0,
        "col": 0,
        "timestamp": "2024-01-15T10:30:15"
      }
    ]
  }
}
```

### 4. Estadísticas
**GET** `/stats`

Obtiene estadísticas generales de todas las partidas.

**Respuesta:**
```json
{
  "total_games": 10,
  "finished_games": 7,
  "incomplete_games": 3,
  "games_with_winner": 5,
  "draw_games": 2
}
```

## Lógica del Juego

### Modos de Juego
- **Modo 1**: Jugador vs CPU (jugador humano siempre es X, CPU siempre es O)
- **Modo 2**: Jugador vs Jugador

### Reglas
1. El tablero es de 3x3
2. Los jugadores alternan turnos (X y O)
3. El primer jugador se determina aleatoriamente
4. Gana quien logre 3 en línea (horizontal, vertical o diagonal)
5. Si el tablero se llena sin ganador, se reinicia automáticamente con nuevo código

### Validaciones
- Solo se puede jugar en casillas vacías
- Solo puede jugar el jugador en su turno
- No se puede continuar jugando una vez terminada la partida
- Las coordenadas deben estar entre 0-2

### CPU
La CPU utiliza una estrategia simple: selecciona aleatoriamente entre las casillas disponibles.

## Sistema de Coordenadas

El tablero usa coordenadas (row, col) donde:
```
(0,0) (0,1) (0,2)
(1,0) (1,1) (1,2)  
(2,0) (2,1) (2,2)
```

## Ejemplos de Uso

### Crear partida de 1 jugador
```bash
curl -X POST "http://localhost:8000/game/new" \
  -H "Content-Type: application/json" \
  -d '{"mode": "1"}'
```

### Realizar jugada
```bash
curl -X POST "http://localhost:8000/game/move" \
  -H "Content-Type: application/json" \
  -d '{"game_code": "A1B2C3D4", "player": "X", "row": 1, "col": 1}'
```

### Ver estadísticas
```bash
curl -X GET "http://localhost:8000/stats"
```

## Estructura del Proyecto

```
├── main.py              # Aplicación principal FastAPI
├── requirements.txt     # Dependencias Python
└── README.md           # Este archivo
```

## Estados del Juego

- **in_progress**: Partida en curso
- **finished**: Partida terminada con ganador
- **draw**: Partida empatada (se reinicia automáticamente)

## Notas Técnicas

- La aplicación almacena datos en memoria (se pierden al reiniciar)
- Para producción, se recomienda usar una base de datos persistente
- Los códigos de partida son únicos de 8 caracteres
- Todas las fechas están en formato ISO 8601

## Desarrollo

Para desarrollo con recarga automática:
```bash
uvicorn main:app --reload
```

## Licencia

MIT License