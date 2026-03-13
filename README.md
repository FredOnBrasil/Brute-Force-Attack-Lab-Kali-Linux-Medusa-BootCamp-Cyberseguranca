# 🔐 Brute Force Attack Lab – Kali Linux + Medusa

## Security Assessment Project

Simulação de ataques de força bruta em serviços de rede utilizando **Kali Linux**, **Medusa** e ambientes vulneráveis como **Metasploitable 2** e **DVWA**.

Este projeto documenta a criação de um **laboratório de segurança ofensiva** para estudo de técnicas de ataque e análise de vulnerabilidades relacionadas a **autenticação fraca e políticas inadequadas de senha**.

⚠️ **Aviso Ético**

Todos os testes foram realizados **exclusivamente em ambiente controlado de laboratório**, utilizando máquinas propositalmente vulneráveis para fins educacionais.

---

# 🎯 Objetivo do Projeto

Demonstrar na prática como ataques de **força bruta e password spraying** podem comprometer sistemas com autenticação fraca.

O projeto cobre:

* Reconhecimento de rede
* Enumeração de serviços
* Ataques de força bruta
* Exploração de autenticação fraca
* Validação de acesso
* Análise de impacto
* Recomendações de mitigação

---

# 🧠 Conceitos de Segurança Explorados

Este laboratório aborda técnicas utilizadas em **Pentest e Red Team**:

| Técnica               | Descrição                               |
| --------------------- | --------------------------------------- |
| Service Enumeration   | Identificação de serviços expostos      |
| Credential Attacks    | Ataques contra autenticação             |
| Brute Force           | Teste de múltiplas senhas               |
| Password Spraying     | Teste de senha comum em vários usuários |
| Authentication Bypass | Exploração de login fraco               |

---

# 🏗️ Arquitetura do Laboratório

Ambiente construído com **VirtualBox** e rede isolada.

```id="i9f6df"
Attacker Machine
(Kali Linux)
192.168.56.101
        │
        │ Host-only Network
        │
Target Machine
(Metasploitable 2)
192.168.56.102
```

Este modelo garante:

* ambiente seguro
* isolamento da rede externa
* simulação realista de ataques internos

---

# 🧰 Ferramentas Utilizadas

| Ferramenta       | Função                             |
| ---------------- | ---------------------------------- |
| Kali Linux       | Sistema operacional para pentest   |
| Medusa           | Ferramenta de brute force          |
| Nmap             | Scanner de rede                    |
| Enum4linux       | Enumeração SMB                     |
| DVWA             | Aplicação web vulnerável           |
| Metasploitable 2 | Sistema propositalmente vulnerável |

---

# ⚙️ Configuração do Ambiente

## 1. Instalação do VirtualBox

Download:

[https://www.virtualbox.org/](https://www.virtualbox.org/)

---

## 2. Configuração das Máquinas Virtuais

### Kali Linux (Attacker)

Requisitos recomendados:

```
RAM: 4 GB
CPU: 2 cores
Disco: 40 GB
Rede: Host-only
```

---

### Metasploitable 2 (Target)

```
RAM: 2 GB
CPU: 1 core
Rede: Host-only
```

Credenciais padrão:

```
msfadmin / msfadmin
```

---

# 🔎 Fase 1 – Reconhecimento

Antes de executar ataques, é necessário identificar os serviços disponíveis no alvo.

Ferramenta utilizada: **Nmap**

Comando:

```bash
nmap -sV -p- 192.168.56.102
```

Este comando realiza:

* varredura completa de portas
* identificação de serviços
* detecção de versões

Exemplo de saída:

```
PORT     SERVICE
21/tcp   FTP
22/tcp   SSH
80/tcp   HTTP
139/tcp  NetBIOS
445/tcp  SMB
```

📷 Evidência:

[]

---

# 🔓 Fase 2 – Ataque de Força Bruta em FTP

O serviço **FTP** frequentemente é vulnerável a ataques de força bruta quando:

* não possui limite de tentativas
* usa senhas fracas

Ferramenta utilizada:

**Medusa**

---

## Wordlist Utilizada

Arquivo:

```
wordlists/passwords.txt
```

Conteúdo exemplo:

```
123456
password
admin
root
toor
msfadmin
```

---

## Execução do Ataque

Comando:

```bash
medusa -h 192.168.56.102 -u msfadmin -P wordlists/passwords.txt -M ftp
```

Parâmetros utilizados:

| Parâmetro | Função            |
| --------- | ----------------- |
| -h        | endereço do alvo  |
| -u        | usuário           |
| -P        | lista de senhas   |
| -M        | módulo do serviço |

---

## Resultado

```
ACCOUNT FOUND: [ftp]
Host: 192.168.56.102
User: msfadmin
Password: msfadmin
```

📷 Evidência:

[]

---

# 🌐 Fase 3 – Ataque em Aplicação Web (DVWA)

DVWA é uma aplicação web criada para treinamento em segurança.

A aplicação permite simular vulnerabilidades reais.

---

## Acesso à Aplicação

```
http://192.168.56.102/dvwa
```

Credenciais padrão:

```
admin / password
```

Após login, o nível de segurança foi configurado para:

```
Security Level: Low
```

Isso permite ataques automatizados.

---

## Teste de Login Automatizado

A tentativa de autenticação ocorre via requisição HTTP:

```
POST /dvwa/login.php
username=admin&password=123456
```

Ferramentas de automação podem repetir essa requisição utilizando wordlists.

---

# 🖥️ Fase 4 – Enumeração SMB

Serviços SMB são frequentemente explorados em ambientes corporativos.

Portas associadas:

```
139
445
```

---

## Enumeração com Enum4linux

Comando:

```bash
enum4linux 192.168.56.102
```

Este processo pode revelar:

* usuários do sistema
* compartilhamentos
* grupos

---

# 🔐 Fase 5 – Password Spraying

Password spraying consiste em testar **uma senha comum contra múltiplos usuários**.

Isso evita bloqueios por tentativas excessivas.

---

## Execução do Ataque

```bash
medusa -h 192.168.56.102 -U wordlists/users.txt -p password -M smbnt
```

Parâmetros:

| Parâmetro | Descrição         |
| --------- | ----------------- |
| -U        | lista de usuários |
| -p        | senha única       |
| -M        | módulo SMB        |

---

# 📂 Estrutura do Repositório

```
bruteforce-security-lab
│
├── README.md
│
├── images
│   ├── nmap_scan.png
│   ├── ftp_bruteforce.png
│   ├── dvwa_login.png
│   └── smb_enum.png
│
├── wordlists
│   ├── passwords.txt
│   └── users.txt
│
└── notes
    └── commands_used.md
```

---

# ⚠️ Análise de Risco

Durante os testes foram identificadas vulnerabilidades relacionadas a:

| Vulnerabilidade          | Impacto                         |
| ------------------------ | ------------------------------- |
| Senhas fracas            | acesso não autorizado           |
| Falta de limite de login | brute force possível            |
| Serviços expostos        | aumento da superfície de ataque |
| Autenticação simples     | comprometimento de contas       |

---

# 🛡️ Recomendações de Segurança

Para mitigar ataques similares:

### Política de Senha Forte

Implementar:

* mínimo 12 caracteres
* letras maiúsculas
* números
* símbolos

---

### Limite de Tentativas

Implementar:

```
Account Lockout
Rate Limiting
Captcha
```

---

### Autenticação Multifator

Uso de **MFA** reduz drasticamente ataques de credential stuffing e brute force.

---

### Monitoramento

Ferramentas recomendadas:

* SIEM
* IDS/IPS
* Fail2Ban

---

# 📚 Referências

Kali Linux
[https://www.kali.org/](https://www.kali.org/)

Medusa
[http://foofus.net/goons/jmk/medusa/medusa.html](http://foofus.net/goons/jmk/medusa/medusa.html)

DVWA
[https://dvwa.co.uk/](https://dvwa.co.uk/)

Nmap
[https://nmap.org/docs.html](https://nmap.org/docs.html)

---

# 👨‍💻 Autor: Frederico Martins Aguiar

Projeto desenvolvido como parte de estudos práticos em **Cybersecurity, Ethical Hacking e Pentest**.

---

# ⭐ Conclusão

Este laboratório demonstra como **mecanismos fracos de autenticação podem ser explorados através de ataques automatizados de força bruta**.

A utilização de ferramentas como **Medusa, Nmap e Enum4linux** permite compreender como atacantes identificam vulnerabilidades e exploram credenciais fracas.

Projetos como este são essenciais para o desenvolvimento de habilidades práticas em **segurança ofensiva e análise de vulnerabilidades**.

---

⭐ **Projeto educacional voltado para estudo de técnicas de pentest em ambiente controlado.**

---
