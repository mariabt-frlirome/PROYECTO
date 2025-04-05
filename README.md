# PROYECTO
FELIPE ROMERO - CAMILA BUITRAGO
#!/usr/bin/env pybricks-micropython
import random
import utime
from pybricks.hubs import EV3Brick
from pybricks.ev3devices import (Motor, TouchSensor, ColorSensor,
                                 InfraredSensor, UltrasonicSensor, GyroSensor)
from pybricks.parameters import Port, Stop, Direction, Button, Color
from pybricks.tools import wait, StopWatch, DataLog
from pybricks.robotics import DriveBase
from pybricks.media.ev3dev import SoundFile, ImageFile

# This program requires LEGO EV3 MicroPython v2.0 or higher.
# Click "Open user guide" on the EV3 extension tab for more information.
# programa para TALLER 1.

def generarNumAletorio(min,max): 
    return random.randint(min, max)

def imprimirHorizontal(km):
    for i in range(km):
        print("-", end="")        

#Funcion para avanzar
def avanzar(movimiento):
    global nivelGasolina, DistanciaRecorrida  # Indicar que modificaremos estas variables globales
    if movimiento == "-": 
        avanzarHorizontal(movimiento)
    elif movimiento == "\\":
        avanzarDiagonal(movimiento)
    elif movimiento == "|":
        avanzarVertical(movimiento)
    # Disminuir gasolina y aumentar distancia
    nivelGasolina -= 0.5
    DistanciaRecorrida += 1  
    #Gestionar Semoforos
    #gestionarSemaforos()
    # Esperar un delay
        # Esperar un delay
    utime.sleep_ms(delay)

#Avanzar horizontal
def avanzarHorizontal(movimiento):
    global Distancia_Horizontal  # Declarar la variable como global # Indicar que modificaremos estas variables globales
    print("-", end="")
    Distancia_Horizontal=Distancia_Horizontal+1 #Aumentar la distancia horizontal

#Avanzar Diagonal
def avanzarDiagonal(movimiento):
    global Distancia_Horizontal  # Declarar la variable como global # Indicar que modificaremos estas variables globales
    print("") #Realizar un salto de linea
    print(" "*Distancia_Horizontal, end="") #Imprimir espacios en blanco
    print("\\", end="") #Imprimir la diagonal
    Distancia_Horizontal=Distancia_Horizontal+1 #Aumentar la distancia horizontal

#Avanzar Vertical
def avanzarVertical(movimiento):
    global Distancia_Horizontal  # Declarar la variable como global # Indicar que modificaremos estas variables globales
    print("") #Realizar un salto de linea
    print(" "*Distancia_Horizontal, end="") #Imprimir espacios en blanco
    print("|", end="") #Imprimir la vertical

#Funcion para obtener el color del semaforo
def getColorSemanforo(color):
    if color==3:
        return "Rojo"
    elif color==2:
        return "Amarillo"
    elif color==1:
        return "Verde"

#Funcion para calculor el maximo de turnos
def maxTurnos(colorr):
    if colorr == 1:
        return 5
    elif colorr == 2:
        return 3
    elif colorr == 3:
        return 1

nivelGasolina=generarNumAletorio(3500,10000)/100
km_avanzar=generarNumAletorio(0,6)


#Recorridos
Recorridos = [
    [12, 7, 12, 0], # Recorrido 1
    [4, 3, 10, 4],  # Recorrido 2
    [10, 7, 6, 0]  # Recorrido 3
]

Vias = [
    {"NUMERO": 1, "NOMBRE": "Autopista norte", "MOVIMIENTO": "-"},
    {"NUMERO": 2, "NOMBRE": "NQS", "MOVIMIENTO": "\\"},
    {"NUMERO": 3, "NOMBRE": "Avenida Boyacá", "MOVIMIENTO": "\\"},
    {"NUMERO": 4, "NOMBRE": "Calle 26", "MOVIMIENTO": "|"},
    {"NUMERO": 5, "NOMBRE": "Calle100", "MOVIMIENTO": "\\"},
    {"NUMERO": 6, "NOMBRE": "Calle170", "MOVIMIENTO": "|"}
]

#RecorridosVias
RecorridosVias = [
    [1, 2, 4, 0], # Recorrido 1
    [1, 6, 3, 4], # Recorrido 2
    [1, 5, 4, 0]  # Recorrido 3
]

#Inicializa los semaforos
ini_semaforos = [None]*5;
for s in range(5): 
    ini_semaforos[s] = generarNumAletorio(1, 3)

Semaforos = [
    {"NUMERO": 1, "VIA": 3, "NOMBRE_VIA": "Avenida Boyacá", "DISTANCIA": 2, "COLOR": getColorSemanforo(ini_semaforos[0]), "ESTADO": ini_semaforos[0], "TURNO": 0, "MAX_TURNOS": maxTurnos(ini_semaforos[0])},
    {"NUMERO": 2, "VIA": 3, "NOMBRE_VIA": "Avenida Boyacá", "DISTANCIA": 5, "COLOR": getColorSemanforo(ini_semaforos[1]), "ESTADO": ini_semaforos[1], "TURNO": 0, "MAX_TURNOS": maxTurnos(ini_semaforos[1])},
    {"NUMERO": 3, "VIA": 2, "NOMBRE_VIA": "NQS",            "DISTANCIA": 4, "COLOR": getColorSemanforo(ini_semaforos[2]), "ESTADO": ini_semaforos[2], "TURNO": 0, "MAX_TURNOS": maxTurnos(ini_semaforos[2])},
    {"NUMERO": 4, "VIA": 4, "NOMBRE_VIA": "Calle 26",       "DISTANCIA": 1, "COLOR": getColorSemanforo(ini_semaforos[3]), "ESTADO": ini_semaforos[3], "TURNO": 0, "MAX_TURNOS": maxTurnos(ini_semaforos[3])},
    {"NUMERO": 5, "VIA": 4, "NOMBRE_VIA": "Calle 26",       "DISTANCIA": 6, "COLOR": getColorSemanforo(ini_semaforos[4]), "ESTADO": ini_semaforos[4], "TURNO": 0, "MAX_TURNOS": maxTurnos(ini_semaforos[4])}
]



#Calcular la distancia total
DistanciaTotal=[sum(fila) for fila in Recorridos]
print("DistanciaTotal:",DistanciaTotal)


print("Gasolina: ",nivelGasolina)
print("km Avanzar: ",km_avanzar)

#Inicio del ciclo principal del programa
DistanciaRecorrida=0
recorridoSel=1
#Distacia Horizontal
Distancia_Horizontal=0
#Definir tiempo para el delay
delay=500
auxTramo=0
tramo=0
while (DistanciaRecorrida<DistanciaTotal[recorridoSel]):
    Distancia=generarNumAletorio(0,6) #Lanzar el dado
    auxMovimiento= next((v["MOVIMIENTO"] for v in Vias if v["NUMERO"] == RecorridosVias[recorridoSel][tramo]), None)
    #Preguntar si la distancia es menor a la distancia del tramo
    if((DistanciaRecorrida+Distancia)<(Recorridos[recorridoSel][tramo]+auxTramo)):       
        for i in range(Distancia): 
            avanzar(auxMovimiento)
    else:
        for i in range((Recorridos[recorridoSel][tramo]+auxTramo)-DistanciaRecorrida): 
            avanzar(auxMovimiento)
        if(DistanciaRecorrida==Recorridos[recorridoSel][tramo]+auxTramo):
            auxTramo=auxTramo+Recorridos[recorridoSel][tramo]
            tramo=tramo+1

print("")
# Create your objects here.
ev3 = EV3Brick()
# Write your program here.
ev3.speaker.beep()

#Imprimir en pantalla
