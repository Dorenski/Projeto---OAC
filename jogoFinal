.data
cartas:     .word 1, 2, 3, 4, 1, 2, 3, 4
estado:     .word 0, 0, 0, 0, 0, 0, 0, 0
pares:      .word 0

msg1:       .asciiz "\nEscolha a primeira carta (1-8): "
msg2:       .asciiz "\nEscolha a segunda carta (1-8): "
acerto:     .asciiz "\n-> Par encontrado!\n"
erro:       .asciiz "\n-> Nao eh par ou movimento invalido!\n"
vitoria:    .asciiz "\nVoce venceu!\n"
espaco:     .asciiz " "
escondido:    .asciiz "X "
linha_sep:  .asciiz "\n--------------------------------------\n"

.text
.globl main

main:
    # Gerar seed aleatoria
    li $v0, 30
    syscall
    move $a1, $a0
    li $a0, 0
    li $v0, 40
    syscall
    
    jal embaralhar_cartas

    li $s0, -1
    li $s1, -1

loop_jogo:
    jal imprimir_tabuleiro

    # Primeira escolha
    li $v0, 4
    la $a0, msg1
    syscall

    li $v0, 5
    syscall
    move $s0, $v0

    jal imprimir_tabuleiro

    # Segunda escolha
    li $v0, 4
    la $a0, msg2
    syscall

    li $v0, 5
    syscall
    move $s1, $v0

    jal imprimir_tabuleiro

    jal verificar_par

    # Verificar se terminou
    lw $t0, pares
    li $t1, 4
    beq $t0, $t1, fim_jogo

    j loop_jogo

# Função embaralhar_cartas
embaralhar_cartas:
    li $t0, 7

loop_embaralhar:
    blez $t0, fim_embaralhar
    
    # Gerar numero aleatorio j entre 0 e i
    li $v0, 42
    li $a0, 0
    addi $a1, $t0, 1
    syscall
    move $t1, $a0

    # Calcular endereços
    sll $t2, $t0, 2
    sll $t3, $t1, 2
    
    la $t4, cartas
    add $t5, $t4, $t2
    add $t6, $t4, $t3

    # Realizar o SWAP
    lw $t7, 0($t5)
    lw $t8, 0($t6)
    sw $t8, 0($t5)
    sw $t7, 0($t6)

    addi $t0, $t0, -1
    j loop_embaralhar

fim_embaralhar:
    jr $ra

# Função imprimir_tabuleiro
imprimir_tabuleiro:
    li $v0, 4
    la $a0, linha_sep
    syscall

    li $t0, 0

print_loop:
    bge $t0, 8, print_fim

    sll $t1, $t0, 2

    # Verificar se é a escolha 1
    addi $t2, $s0, -1
    beq $t0, $t2, mostrar_valor

    # Verificar se é a escolha 2
    addi $t3, $s1, -1
    beq $t0, $t3, mostrar_valor

    # Verificar estado permanente
    la $t4, estado
    add $t5, $t4, $t1
    lw $t6, 0($t5)
    beq $t6, 1, mostrar_valor

    li $v0, 4
    la $a0, escondido
    syscall
    j proximo

mostrar_valor:
    la $t4, cartas
    add $t5, $t4, $t1
    lw $a0, 0($t5)
    li $v0, 1
    syscall

    li $v0, 4
    la $a0, espaco
    syscall

proximo:
    # Verificar se precisa quebrar linha
    addi $t7, $t0, 1
    li   $t8, 4
    div  $t7, $t8
    mfhi $t9

    bne  $t9, $zero, continuar

    li $v0, 11
    li $a0, 10
    syscall

continuar:
    addi $t0, $t0, 1
    j print_loop

print_fim:
    jr $ra

# Função verificar_par
verificar_par:
    # Converter entradas para indices
    addi $t0, $s0, -1
    addi $t1, $s1, -1

    # Verifica se as entradas estao no intervalo [0,7]
    bltz $t0, nao_par
    bgt  $t0, 7, nao_par
    bltz $t1, nao_par
    bgt  $t1, 7, nao_par

    # Validar se o usuario escolheu a mesma carta duas vezes
    beq $t0, $t1, nao_par

    sll $t0, $t0, 2
    sll $t1, $t1, 2

    # Validar se alguma das cartas ja foi revelada
    la $t7, estado
    
    add $t8, $t7, $t0
    lw  $t9, 0($t8)
    beq $t9, 1, nao_par

    add $t8, $t7, $t1
    lw  $t9, 0($t8)
    beq $t9, 1, nao_par

    # Ler os valores das cartas
    la $t2, cartas
    add $t3, $t2, $t0
    lw $t4, 0($t3)

    add $t5, $t2, $t1
    lw $t6, 0($t5)

    # Verifica se são iguais
    bne $t4, $t6, nao_par

    # Marcar nos estados que o par foi achado
    la $t7, estado
    add $t8, $t7, $t0
    li $t9, 1
    sw $t9, 0($t8)

    add $t8, $t7, $t1
    sw $t9, 0($t8)

    # Incrementar total de pares achados
    lw $t0, pares
    addi $t0, $t0, 1
    sw $t0, pares

    li $v0, 4
    la $a0, acerto
    syscall
    
    li $s0, -1
    li $s1, -1
    jr $ra

nao_par:
    li $v0, 4
    la $a0, erro
    syscall
    
    # Reseta as escolhas do usuario para a proxima rodada
    li $s0, -1
    li $s1, -1
    jr $ra

# FIM
fim_jogo:
    # Imprime mensagem de vitoria
    li $v0, 4
    la $a0, vitoria
    syscall

    # Encerra o programa
    li $v0, 10
    syscall
