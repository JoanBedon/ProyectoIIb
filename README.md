# ProyectoIIb
#include <iostream>
#include <iomanip>
#include <fstream>
#include <string>
#include <cstdlib>
#include <ctime>
using namespace std;

struct Jugador {
    string nombre;
    int score;
};

void IngresarJugador(Jugador jugadores[], int &jugador) {
    cout << "Ingrese el número de jugadores: ";
    cin >> jugador;

    if (jugador > 20 || jugador < 1) {
        cout << "Número de jugadores no válido. Debe ser entre 1 y 20" << endl;
        exit(1);
    }

    for (int i = 0; i < jugador; i++) {
        cout << "Ingrese el nombre del jugador " << i + 1 << ": ";
        cin >> jugadores[i].nombre;
        jugadores[i].score = 0;
    }
}

void ImprimirTablero(int tabla[4][4]) {
    cout << "Estado actual del tablero:" << endl;
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            cout << setw(4);
            if (tabla[i][j] != 0) {
                cout << "[" << tabla[i][j] << "]";
            } else {
                cout << "[  ]";
            }
        }
        cout << endl;
    }
    cout << endl;
}

void ImprimirTableroOrdenado() {
    cout << "Tablero ordenado:" << endl;
    int numeros[15];

    // Llenar el arreglo con los números del 1 al 15
    for (int i = 0; i < 15; i++) {
        numeros[i] = i + 1;
    }

    // Mostrar el tablero ordenado
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            cout << setw(4);
            if (i == 3 && j == 3) {
                cout << "[  ]";
            } else {
                cout << "[" << numeros[i * 4 + j] << "]";
            }
        }
        cout << endl;
    }
    cout << endl;
}

void InicializarTablero(int tabla[4][4]) {
    int numeros[15];
    srand(static_cast<unsigned int>(time(nullptr)));

    // Llenar el arreglo con los números del 1 al 15
    for (int i = 0; i < 15; i++) {
        numeros[i] = i + 1;
    }

    // Barajar los números
    for (int i = 14; i > 0; i--) {
        int j = rand() % (i + 1);
        swap(numeros[i], numeros[j]);
    }

    // Llenar el tablero con los números barajados y dejar un espacio
    int k = 0;
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            if (i == 3 && j == 3) {
                tabla[i][j] = 0; // Espacio vacío
            } else {
                tabla[i][j] = numeros[k++];
            }
        }
    }
}

bool ComprobarOrden(int tabla[4][4]) {
    int k = 1;
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            if (tabla[i][j] != 0 && tabla[i][j] != k++) {
                return false;
            }
        }
    }
    return true;
}

int JugarTurno(int tabla[4][4], int &x, int &y, int &movimientos) {
    char caracter;

    cout << "Ingrese su movimiento (w/a/s/d para arriba/izquierda/abajo/derecha, 0 para salir): ";
    cin >> caracter;

    switch (caracter) {
        case 'w':   // arriba
            if (y < 3) {
                swap(tabla[y][x], tabla[y + 1][x]);
                y++;
                movimientos++;
            } else {
                cout << "Fuera de los límites" << endl;
            }
            break;
        case 's':   // abajo
            if (y > 0) {
                swap(tabla[y][x], tabla[y - 1][x]);
                y--;
                movimientos++;
            } else {
                cout << "Fuera de los límites" << endl;
            }
            break;
        case 'a':   // izquierda
            if (x < 3) {
                swap(tabla[y][x], tabla[y][x + 1]);
                x++;
                movimientos++;
            } else {
                cout << "Fuera de los límites" << endl;
            }
            break;
        case 'd':   // derecha
            if (x > 0) {
                swap(tabla[y][x], tabla[y][x - 1]);
                x--;
                movimientos++;
            } else {
                cout << "Fuera de los límites" << endl;
            }
            break;
        case '0':   // Salida
            break;
        default:
            cout << "Carácter incorrecto" << endl;
            break;
    }

    // Mostrar el tablero después de cada movimiento
    ImprimirTablero(tabla);

    // Mostrar el puntaje después de cada movimiento
    cout << "Movimientos realizados: " << movimientos << endl;

    return movimientos;
}

void MostrarPuntajes(Jugador jugadores[], int jugador) {
    cout << "Puntajes de los jugadores: " << endl;
    for (int i = 0; i < jugador; i++) {
        cout << "Jugador: " << jugadores[i].nombre << ", Puntaje: " << jugadores[i].score << endl;
    }
}

void Jugar(int tabla[4][4], Jugador jugadores[], int jugador) {
    for (int i = 0; i < jugador; i++) {
        InicializarTablero(tabla); // Barajar y reiniciar el tablero al inicio de cada turno
        ImprimirTablero(tabla);

        cout << "Turno del jugador " << jugadores[i].nombre << endl;
        int x = 3, y = 3, movimientos = 0;
        char decision;

        do {
            int movimientos_jugador = JugarTurno(tabla, x, y, movimientos);
            jugadores[i].score += movimientos_jugador;

            // Comprobar si el tablero está ordenado después de 20 movimientos
            if (movimientos == 20) {
                cout << "¿Desea ordenar el tablero? (s/n): ";
                cin >> decision;
                if (decision == 's') {
                    if (ComprobarOrden(tabla)) {
                        cout << "¡El tablero ya está ordenado correctamente!" << endl;
                        break;
                    } else {
                        cout << "Tablero ordenado. Fin del turno del jugador " << jugadores[i].nombre << endl;
                            ImprimirTableroOrdenado();
                        break;
                    }
                }
            }
        } while (true);
    }


}

int main() {
    Jugador jugadores[20];
    int jugador = 0;
    IngresarJugador(jugadores, jugador);

    int tabla[4][4];

    do {
        cout << "Menu de opciones:" << endl;
        cout << "1. Jugar" << endl;
        cout << "2. Puntajes" << endl;
        cout << "3. Salir" << endl;
        cout << "4. Créditos" << endl;
        int opcion;
        cout << "Ingrese su elección (1 al 4): ";
        cin >> opcion;

        switch (opcion) {
            case 1:
                if (jugador > 0) {
                    Jugar(tabla, jugadores, jugador);
                } else {
                    cout << "Ingrese al menos un jugador primero." << endl;
                }
                break;
            case 2: 
                MostrarPuntajes(jugadores, jugador);
                break;
            case 3: 
                cout << "Saliendo..." << endl;
                return 0;
            case 4:
                cout << "Créditos: Joan Bedon y Zamira Jimenez" << endl;
                break;
            default:
                cout << "Opción no válida. Ingrese un número entre 1 y 4." << endl;
                break;
        }
    } while (true);
    return 0;
}
