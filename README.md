from datetime import datetime
from collections import defaultdict

# Dicionário para usuários e contas
usuarios = {}
contas = defaultdict(list)
transacoes = defaultdict(list)

# Funções de utilidade
def obter_data_hora():
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

def novo_usuario():
    cpf = input("Digite o CPF do usuário (somente números): ")
    if cpf in usuarios:
        print("Usuário já existe.")
    else:
        nome = input("Digite o nome do usuário: ")
        data_nascimento = input("Digite a data de nascimento (AAAA-MM-DD): ")
        endereco = input("Digite o endereço do usuário: ")
        usuarios[cpf] = {"nome": nome, "data_nascimento": data_nascimento, "endereco": endereco}
        print("Usuário cadastrado com sucesso!")

def nova_conta():
    cpf = input("Digite o CPF do titular da conta: ")
    if cpf not in usuarios:
        print("Usuário não encontrado.")
    else:
        numero_conta = len(contas[cpf]) + 1
        contas[cpf].append({"numero": numero_conta, "saldo": 0})
        print(f"Conta número {numero_conta} criada para o usuário {cpf}.")

def listar_contas():
    cpf = input("Digite o CPF do usuário: ")
    if cpf in contas:
        for conta in contas[cpf]:
            print(f"Conta {conta['numero']} - Saldo: {conta['saldo']}")
    else:
        print("Nenhuma conta encontrada para este usuário.")

def depositar():
    cpf = input("Digite o CPF do usuário: ")
    conta_num = int(input("Digite o número da conta: "))
    valor = float(input("Digite o valor para depósito: "))
    if cpf in contas and conta_num <= len(contas[cpf]):
        data_hora = obter_data_hora()
        if len(transacoes[cpf]) >= 10:
            print("Limite de 10 transações diárias alcançado.")
            return
        contas[cpf][conta_num - 1]['saldo'] += valor
        transacoes[cpf].append((data_hora, f"Depósito de R${valor:.2f} na conta {conta_num}."))
        print(f"Depósito de R${valor:.2f} realizado com sucesso.")
    else:
        print("Conta não encontrada.")

def sacar():
    cpf = input("Digite o CPF do usuário: ")
    conta_num = int(input("Digite o número da conta: "))
    valor = float(input("Digite o valor para saque: "))
    if cpf in contas and conta_num <= len(contas[cpf]):
        saldo = contas[cpf][conta_num - 1]['saldo']
        data_hora = obter_data_hora()
        if len(transacoes[cpf]) >= 10:
            print("Limite de 10 transações diárias alcançado.")
            return
        if saldo >= valor:
            contas[cpf][conta_num - 1]['saldo'] -= valor
            transacoes[cpf].append((data_hora, f"Saque de R${valor:.2f} da conta {conta_num}."))
            print(f"Saque de R${valor:.2f} realizado com sucesso.")
        else:
            print("Saldo insuficiente.")
    else:
        print("Conta não encontrada.")

def extrato():
    cpf = input("Digite o CPF do usuário: ")
    if cpf in transacoes:
        print(f"\nExtrato de transações para o usuário {cpf}:")
        for data_hora, transacao in transacoes[cpf]:
            print(f"{data_hora} - {transacao}")
    else:
        print("Nenhuma transação encontrada para este usuário.")

# Sistema principal
def sistema_bancario():
    while True:
        print("\nOpções:")
        print("[D] Depositar")
        print("[S] Sacar")
        print("[E] Extrato")
        print("[NC] Nova conta")
        print("[LC] Listar contas")
        print("[NU] Novo usuário")
        print("[Q] Sair")
        
        opcao = input("Escolha uma opção: ").upper()

        if opcao == 'D':
            depositar()
        elif opcao == 'S':
            sacar()
        elif opcao == 'E':
            extrato()
        elif opcao == 'NC':
            nova_conta()
        elif opcao == 'LC':
            listar_contas()
        elif opcao == 'NU':
            novo_usuario()
        elif opcao == 'Q':
            print("Saindo do sistema.")
            break
        else:
            print("Opção inválida. Tente novamente.")

# Executando o sistema
sistema_bancario()
