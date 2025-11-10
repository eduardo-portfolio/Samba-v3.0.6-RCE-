# Recon em Nessus & Ataque RCE em SMB no lab Metasploitable (Nessus & Kali Linux)

## Objetivo
Avaliação de vulnerabilidades com Nessus no VMware com ISO Metasploitable, enumeração de shares via smbmap/smbclient, confirmação de Samba versão 3.0.6 e validação prática com Metasploit. Obtive acesso controlado na máquina vulnerável.

### Habilidades Aprendidas 

- Gerenciamento de Nessus
- Configuração de NAT/Host-Only
- Buffer/heap overflow

### Ferramentas Usadas 

- Nessus
- Kali Linux (VMware Pro)
- Metasploit

## Meus passos

Instalei o Nessus Essentials no host Windows para varredura de vulnerabilidades na máquina.  

<img width="1913" height="1031" alt="image" src="https://github.com/user-attachments/assets/126c556f-517c-49bc-86c2-8f462be4771f" />

Descompactei a VM Metasploitable (.7zip)

<img width="229" height="164" alt="image" src="https://github.com/user-attachments/assets/4b34e6f6-87b7-4eb3-9138-8ac902648625" />

e a carreguei no VMware.

<img width="1916" height="1072" alt="image" src="https://github.com/user-attachments/assets/99846b48-e136-4711-82da-c7de19353b08" />

No Nessus, criei e executei um Basic Network Scan apontando para o IP da máquina.

<img width="1451" height="730" alt="image" src="https://github.com/user-attachments/assets/a37e1736-28f7-4b99-8f4b-15bb80df22ce" />


Analisei o relatório final do Nessus.

<img width="1723" height="625" alt="image" src="https://github.com/user-attachments/assets/658bff0f-0b7a-40e3-b4e3-80c0caba596b" />

Várias vulnerabilidades que podem ser exploradas, mas as que me chamoram a atenção foram as do SMB.

<img width="1919" height="958" alt="image" src="https://github.com/user-attachments/assets/49d7c0a3-9b7f-4cec-80c3-dfbbaaebee16" />

Do Kali, confirmei conectividade:

<img width="536" height="318" alt="image" src="https://github.com/user-attachments/assets/40481126-6d05-4665-93f8-f60c9ca37173" />

Enumerei shares e permissões, meu objetivo era encontrar algum arquivo interessante:

**Comando 1:** smbmap -H 192.168.140.130 -u msfadmin -p msfadmin  **Comando 2:**
smbclient -L //192.168.140.130 -U msfadmin%msfadmin


<img width="1127" height="283" alt="image" src="https://github.com/user-attachments/assets/f0b4c5a8-7643-473b-b453-f48d72067786" />

Como boa prática, procuro shares de usuários com nomes padrões

<img width="1138" height="436" alt="image" src="https://github.com/user-attachments/assets/fadcc695-a288-482d-b440-7689dbd748a3" />

Abri sessão interativa no share msfadmin:

<img width="610" height="293" alt="image" src="https://github.com/user-attachments/assets/bd3669de-450b-4dd8-b5de-59a0a5e437d7" />

Já que não encontrei nenhum arquivo para extrair no SMB, decido explorar a vulnerabilidade que esta antiga versão do Samba possui, para isto executo o Searchsploit para relembrar as vulnerabilidades que podem ser exploradas nesta versão do software. 


<img width="454" height="217" alt="image" src="https://github.com/user-attachments/assets/cd65c15c-c2b9-4a0e-8bca-305e54796bce" />

Com base score de 10.0, esta versão do Samba possui falhas graves de segurança, e uma delas permite que atacantes executem comandos remotamente, e é esta falha que irei explorar. 

<img width="1047" height="544" alt="image" src="https://github.com/user-attachments/assets/307b5a13-976d-4d55-8014-49b7fb4e58bd" />

Por ser uma vulnerabilidade antiga, é interessante checar no Metasploit se esta vulnerabilidade já não possui um Exploit pronto para ser utilizado, por sorte, encontrei um que faz exatamente o que eu quero: criar uma sessão shell e obter controle da máquina. 

<img width="934" height="82" alt="image" src="https://github.com/user-attachments/assets/82db9bfa-052a-47ed-abce-42d9bffea4f2" />

Após configurar os IPs no MSFCONSOLE, obtive acesso fácil à máquina vulnerável. 

<img width="142" height="76" alt="image" src="https://github.com/user-attachments/assets/451ec3b1-fecb-41d9-84dd-d12e6b9e1dc0" />


Para prevenir ataques ao SMB, atualize imediatamente o Samba para sua versão mais recente, desabilitar o serviço nmbd e as portas NetBIOS (137-139/UDP/TCP), restrinja o SMB apenas à porta 445/TCP.



