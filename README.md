#include <stdio.h>
#include "pico/stdlib.h"

// Configuração dos pinos
#define BUZZER_PIN 15
#define ROWS 4
#define COLS 4

// Definição das linhas e colunas do teclado
uint linhas[ROWS] = {0, 1, 2, 3};   // GPIOs para as linhas
uint colunas[COLS] = {4, 5, 6, 7}; // GPIOs para as colunas

// Mapeamento do teclado
char MAPA_TECLAS[ROWS][COLS] = {
    {'1', '2', '3', 'A'},
    {'4', '5', '6', 'B'},
    {'7', '8', '9', 'C'},
    {'*', '0', '#', 'D'}
};

// Função para inicializar os pinos do teclado
void inicia_teclado() {
    for (int i = 0; i < ROWS; i++) {
        gpio_init(linhas[i]);
        gpio_set_dir(linhas[i], GPIO_OUT);
    }
    for (int j = 0; j < COLS; j++) {
        gpio_init(colunas[j]);
        gpio_set_dir(colunas[j], GPIO_IN);
        gpio_pull_down(colunas[j]);
    }
}

// Função para verificar a tecla pressionada
char verifica_tecla() {
    for (int i = 0; i < ROWS; i++) {
        gpio_put(linhas[i], 1); // Ativa a linha
        for (int j = 0; j < COLS; j++) {
            if (gpio_get(colunas[j])) { // Verifica se a coluna está ativada
                gpio_put(linhas[i], 0); // Desativa a linha antes de retornar
                return MAPA_TECLAS[i][j]; // Retorna a tecla pressionada
            }
        }
        gpio_put(linhas[i], 0); // Garante que a linha seja desativada
    }
    return '\0'; // Nenhuma tecla pressionada
}

// Função para acionar o buzzer
void aciona_buzzer() {
    gpio_put(BUZZER_PIN, 1);
    sleep_ms(500); // Liga o buzzer por 500 ms
    gpio_put(BUZZER_PIN, 0);
}

int main() {
    stdio_init_all();

    // Inicializa o teclado e o buzzer
    inicia_teclado();
    gpio_init(BUZZER_PIN);
    gpio_set_dir(BUZZER_PIN, GPIO_OUT);

    while (true) {
        char tecla = verifica_tecla();
        if (tecla == '#') { // Verifica se a tecla pressionada é '#'
            aciona_buzzer();
        }
    }
}
