# Atividade-N3
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NOME 50
#define MAX_CPF 12 // 11 caracteres + 1 para o terminador nulo

typedef struct {
    char nome[MAX_NOME];
    int idade;
    char cpf[MAX_CPF];
} Pessoa;

Pessoa *cadastros = NULL;
int totalPessoas = 0;

void limparBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

int lerInteiro(const char *mensagem) {
    int valor;
    while (1) {
        printf("%s", mensagem);
        if (scanf("%d", &valor) == 1) {
            limparBuffer();
            return valor;
        } else {
            printf("Entrada inválida. Tente novamente.\n");
            limparBuffer();
        }
    }
}

void adicionarCadastro() {
    if (totalPessoas >= 100) {
        printf("Limite de cadastros atingido.\n");
        return;
    }

    Pessoa *novoCadastro = realloc(cadastros, (totalPessoas + 1) * sizeof(Pessoa));
    if (novoCadastro == NULL) {
        printf("Erro ao alocar memória.\n");
        return;
    }

    cadastros = novoCadastro;

    printf("Digite o nome (máximo %d caracteres): ", MAX_NOME - 1);
    limparBuffer();
    fgets(cadastros[totalPessoas].nome, MAX_NOME, stdin);
    cadastros[totalPessoas].nome[strcspn(cadastros[totalPessoas].nome, "\n")] = '\0'; // Remove o \n do final

    cadastros[totalPessoas].idade = lerInteiro("Digite a idade (número positivo): ");
    while (cadastros[totalPessoas].idade <= 0) {
        printf("Idade inválida. Deve ser um número positivo.\n");
        cadastros[totalPessoas].idade = lerInteiro("Digite novamente a idade: ");
    }

    printf("Digite o CPF (máximo %d caracteres): ", MAX_CPF - 1);
    fgets(cadastros[totalPessoas].cpf, MAX_CPF, stdin);
    cadastros[totalPessoas].cpf[strcspn(cadastros[totalPessoas].cpf, "\n")] = '\0'; // Remove o \n do final

    totalPessoas++;
    printf("Cadastro adicionado com sucesso.\n");
}

void listarCadastros() {
    if (totalPessoas == 0) {
        printf("Nenhum cadastro encontrado.\n");
        return;
    }

    for (int i = 0; i < totalPessoas; i++) {
        printf("Cadastro #%d:\n", i);
        printf("Nome: %s\n", cadastros[i].nome);
        printf("Idade: %d\n", cadastros[i].idade);
        printf("CPF: %s\n\n", cadastros[i].cpf);
    }
}

void deletarCadastro() {
    if (totalPessoas == 0) {
        printf("Nenhum cadastro para deletar.\n");
        return;
    }

    int indice = lerInteiro("Digite o índice do cadastro a ser deletado: ");
    if (indice < 0 || indice >= totalPessoas) {
        printf("Índice inválido.\n");
        return;
    }

    memmove(&cadastros[indice], &cadastros[indice + 1], (totalPessoas - indice - 1) * sizeof(Pessoa));
    totalPessoas--;

    if (totalPessoas == 0) {
        free(cadastros);
        cadastros = NULL;
    } else {
        Pessoa *novoCadastro = realloc(cadastros, totalPessoas * sizeof(Pessoa));
        if (novoCadastro != NULL) {
            cadastros = novoCadastro;
        }
    }

    printf("Cadastro deletado com sucesso.\n");
}

void liberarMemoria() {
    free(cadastros);
    cadastros = NULL;
    totalPessoas = 0;
}

int main() {
    int opcao;

    do {
        printf("\nMenu:\n");
        printf("1. Adicionar Cadastro\n");
        printf("2. Listar Cadastros\n");
        printf("3. Deletar Cadastro\n");
        printf("4. Sair\n");
        printf("Escolha uma opção: ");

        opcao = lerInteiro("");

        switch (opcao) {
            case 1:
                adicionarCadastro();
                break;
            case 2:
                listarCadastros();
                break;
            case 3:
                deletarCadastro();
                break;
            case 4:
                liberarMemoria();
                printf("Encerrando o programa...\n");
                break;
            default:
                printf("Opção inválida. Tente novamente.\n");
        }
    } while (opcao != 4);

    return 0;
}

Passo a passo da explicacção do codigo

1-Definição da estrutura e constantes:

A estrutura Pessoa é usada para representar cada cadastro, contendo os campos nome, idade e cpf.
Constantes definem os tamanhos máximos de nome e CPF.

2-Funções auxiliares:

limparBuffer: Limpa o buffer de entrada para evitar problemas com entradas inválidas.
lerInteiro: Lê e valida a entrada de números inteiros para evitar erros de entrada.

3-Operações principais:

Adicionar Cadastro:

Usa realloc para ajustar dinamicamente a memória ao adicionar novos cadastros.
Valida as entradas do usuário, garantindo a conformidade dos dados.
Listar Cadastros:

Itera pelo vetor de cadastros para exibir os dados armazenados.
Deletar Cadastro:

Usa memmove para reorganizar os cadastros após a exclusão.
Reduz a memória alocada com realloc ou libera completamente se não houver cadastros restantes.

4-Liberação de memória:

A função liberarMemoria garante que toda a memória dinâmica seja liberada ao sair do programa.
