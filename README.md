# Doações

<table>
		<tr>
			<td> <!-- PagSeguro -->
				<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
				<h3><a id="user-content-features" class="anchor" href="#features" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" role="img" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1h-1c-1.5 0-3-1.69-3-3.5s1.55-3.5 3-3.5h4c1.45 0 3 1.69 3 3.5 0 1.41-0.91 2.72-2 3.25v-1.16c0.58-0.45 1-1.27 1-2.09 0-1.28-1.02-2.5-2-2.5H4c-0.98 0-2 1.22-2 2.5s1 2.5 2 2.5z m9-3h-1v1h1c1 0 2 1.22 2 2.5s-1.02 2.5-2 2.5H9c-0.98 0-2-1.22-2-2.5 0-0.83 0.42-1.64 1-2.09v-1.16c-1.09 0.53-2 1.84-2 3.25 0 1.81 1.55 3.5 3 3.5h4c1.45 0 3-1.69 3-3.5s-1.5-3.5-3-3.5z"></path></svg></a><div align="center">PagSeguro</div></h3>
				<a href="https://pag.ae/bljJm47"><img src="https://stc.pagseguro.uol.com.br/public/img/botoes/doacoes/120x53-doar.gif"></a>
      </td>
			<td> <!-- PayPal -->
				<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
				<h3><a id="user-content-features" class="anchor" href="#features" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" role="img" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1h-1c-1.5 0-3-1.69-3-3.5s1.55-3.5 3-3.5h4c1.45 0 3 1.69 3 3.5 0 1.41-0.91 2.72-2 3.25v-1.16c0.58-0.45 1-1.27 1-2.09 0-1.28-1.02-2.5-2-2.5H4c-0.98 0-2 1.22-2 2.5s1 2.5 2 2.5z m9-3h-1v1h1c1 0 2 1.22 2 2.5s-1.02 2.5-2 2.5H9c-0.98 0-2-1.22-2-2.5 0-0.83 0.42-1.64 1-2.09v-1.16c-1.09 0.53-2 1.84-2 3.25 0 1.81 1.55 3.5 3 3.5h4c1.45 0 3-1.69 3-3.5s-1.5-3.5-3-3.5z"></path></svg></a><div align="center">PayPal</div></h3>
				<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=7VVS675TLJHUL&lc=BR&item_name=Eracydes%20Lima%20Carvalho%20Junior&currency_code=BRL&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHosted"><img src="https://www.paypalobjects.com/pt_BR/BR/i/btn/btn_donateCC_LG.gif"></a>
			</td>
		</tr>
</table>



# Email_Gmail_ZABBIX
Neste post, aprenderemos a enviar email autenticado pelo ZABBIX através do Gmail.<br>
O "How to" foi testado no ZABBIX 2.4 e no 3.0 com base em Debian.

# Iniciando

Instale os pacotes abaixo:<br>
<blockquote> <p>CentOS 6.x e 7</p> </blockquote>
<code>yum install -y cyrus-sasl cyrus-sasl-devel cyrus-sasl-gssapi cyrus-sasl-md5 cyrus-sasl-plain mutt postfix</code><br>
<code>chkconfig postfix on</code><br><br>
<!--<code>service postfix start</code><br><br>-->
<blockquote> <p>Debian</p> </blockquote>
<code>sudo apt-get install -y postfix mailutils libsasl2-2 ca-certificates libsasl2-modules mutt mutt-patched</code><br><br>
<i>Irá aparecer uma tela, selecione a opção Site Internet e selecione OK.</i><br>
<i>Digite o nome do seu servidor de e-mail, EX: monitoramento.com</i><br>
<i>Em seguida, entre no diretório de configuração do Postfix, faça o backup do arquivo de configuração:</i><br>

<code>cd /etc/postfix/ ; sudo mv main.cf main.cf.old</code><br><br>

<i>Crie um novo arquivo de configuração para Postfix: </i><br>

<code>sudo vi main.cf </code><br><br>

<i>Nele, coloque somente as seguintes linhas: </i><br>

<code>relayhost = [smtp.gmail.com]:587</code><br>
<code>smtp_tls_loglevel = 1</code><br>
<code>smtp_use_tls = yes</code><br>
<code>smtpd_tls_received_header = yes</code><br>
<code>smtp_sasl_auth_enable = yes</code><br>
<code>smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd</code><br>
<code>smtp_sasl_security_options = noanonymous</code><br>
<code>smtp_sasl_tls_security_options = noanonymous</code><br><br>

<i>Crie o arquivo "sasl_passwd", contendo o servidor SMTP do Google e a conta que será utilizada para envio dos e-mails. </i><br>

<code>sudo vi sasl_passwd </code><br>
<code>[smtp.gmail.com]:587 SeuEmail@gmail.com:SenhaDoEmail</code><br><br>

<i>Em seguida, rodamos o comando "postmap" no arquivo "sasl_passwd" e no "main.cf", para que eles possam ser reconhecidos e utilizados pelo Postfix:</i><br>

<code>sudo postmap /etc/postfix/sasl_passwd ; sudo postmap /etc/postfix/main.cf</code><br><br>

<i>Dar permissão</i><br>

<code>sudo chown zabbix:zabbix sasl_passwd ; sudo chmod 600 sasl_passwd</code><br><br>

<i>Valide as permissões</i> (caso dê "arquivo não encontrado" ou qualquer erro, pode ignorar e seguir)<br>

<code>sudo cat /etc/ssl/certs/Thawte_Premium_Server_CA.pem | sudo tee -a /etc/postfix/cacert.pem</code><br><br>

<i>Reinicie o serviço do Postfix: </i><br>

<code>sudo service postfix restart </code><br><br>

<i>Pronto, agora você está com tudo certo para enviar e-mails através do shell. Faça um teste: </i><br>

<code>echo 'Teste.' | mutt -s 'Teste de envio pelo shell' SeuEmail@gmail.com</code><br><br>

Cheque a caixa de entrada do seu e-mail, ou o aquivo de log.<br>
Perceba o "status=", deve estar "status=sent".<br><br>

<blockquote> <p>CentOS 6.x e 7</p> </blockquote>
<code>tail -f /var/log/maillog</code><br><br>

<blockquote> <p>Debian</p> </blockquote>
<code>tail -f /var/log/mail.log</code><br><br>




# OBS

<h3><a id="user-content-features" class="anchor" href="#features" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" role="img" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1h-1c-1.5 0-3-1.69-3-3.5s1.55-3.5 3-3.5h4c1.45 0 3 1.69 3 3.5 0 1.41-0.91 2.72-2 3.25v-1.16c0.58-0.45 1-1.27 1-2.09 0-1.28-1.02-2.5-2-2.5H4c-0.98 0-2 1.22-2 2.5s1 2.5 2 2.5z m9-3h-1v1h1c1 0 2 1.22 2 2.5s-1.02 2.5-2 2.5H9c-0.98 0-2-1.22-2-2.5 0-0.83 0.42-1.64 1-2.09v-1.16c-1.09 0.53-2 1.84-2 3.25 0 1.81 1.55 3.5 3 3.5h4c1.45 0 3-1.69 3-3.5s-1.5-3.5-3-3.5z"></path></svg></a>NUNCA mantenha o sendmail (sem o 'e') junto com o postfix, desinstale-o IMEDIATAMENTE!!!!</h3>
