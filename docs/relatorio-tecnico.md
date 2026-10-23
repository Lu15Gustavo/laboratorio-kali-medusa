# Relatório técnico — Kali Linux e Medusa

Autor: Luís Gustavo Barbosa Gonçalves.

## 1. Resumo

O projeto consolida os documentos Relatorio.docx e Shutdown-0.docx e três capturas fornecidas pelo autor. Seu objetivo é documentar aprendizados sobre autenticação, inspeção de formulários e comandos Linux no contexto do desafio DIO.

A análise é documental. Não foram executados novos testes nem verificados os sistemas mostrados nas capturas durante a elaboração deste relatório.

## 2. Relação com o desafio

O enunciado sugere VMs com Kali Linux e Metasploitable 2, testes em FTP, formulário web/DVWA e password spraying em SMB, além de documentação e mitigação. Ele permite adaptar a entrega aos estudos e experiências disponíveis.

As evidências recebidas sustentam um resultado reportado pelo Medusa em FTP, inspeção de requisição web e listagem de compartilhamentos SMB. Elas não comprovam a conclusão integral dos cenários sugeridos.

## 3. Fontes e rastreabilidade

| Fonte | Contribuição |
| --- | --- |
| Imagem do enunciado DIO | Objetivos e requisitos da entrega |
| Relatorio.docx | Experiência com Medusa, filtragem e inspeção de parâmetros |
| Shutdown-0.docx | Anotações sobre arquivos, usuários, grupos e permissões Linux |
| images/01-medusa-ftp.jpg | Resultado FTP apresentado pelo Medusa |
| images/02-inspecao-login-web.jpg | Inspeção da requisição de login |
| images/03-listagem-smb.jpg | Listagem de compartilhamentos via smbclient |

Os documentos originais são preservados sem alterações na pasta documentos-originais. Suas anotações contêm imprecisões e não devem ser tratadas como manual de comandos validado.

## 4. Ambiente observado e ambiente proposto

As capturas mostram um terminal e acesso ao endereço privado 192.168.56.101. A listagem SMB identifica o servidor como METASPLOITABLE e exibe uma descrição Samba 3.0.20-Debian. Esses são dados apresentados pela saída, não um inventário independente do sistema.

O uso efetivo de VirtualBox, as versões das VMs e o modo de rede não podem ser confirmados apenas pelas capturas. Rede interna e host-only são modos distintos: host-only inclui o host na rede virtual; rede interna conecta as VMs participantes da mesma rede virtual.

## 5. Análise das evidências

### 5.1. FTP e Medusa

A primeira imagem apresenta o comando Medusa direcionado ao alvo 192.168.56.101, com listas users.txt e pass.txt, módulo ftp e filtragem da saída por SUCCESS. O retorno exibido informa ACCOUNT FOUND e identifica a combinação de laboratório msfadmin/msfadmin.

Conclusão sustentada: o Medusa reportou uma credencial encontrada para FTP. A captura não apresenta o conteúdo das listas, a contagem de tentativas ou uma sessão FTP independente. Como a saída foi filtrada, mensagens não correspondentes ao filtro não aparecem na imagem. Não é possível calcular taxa de sucesso ou duração do teste.

### 5.2. Inspeção de formulário web

A segunda imagem mostra as ferramentas de desenvolvedor do navegador, uma requisição POST para login.php no mesmo endereço privado e a inspeção de campos do formulário. Isso complementa a observação do autor sobre o uso de F12 para identificar parâmetros.

Conclusão sustentada: houve inspeção de uma requisição de login. A imagem não comprova autenticação bem-sucedida, execução do Medusa contra o formulário ou identificação inequívoca da aplicação como DVWA.

O rascunho de comando web presente no DOCX contém marcadores genéricos e sintaxe não validada. Ele não foi convertido em instrução pronta para execução.

### 5.3. SMB e listagem de compartilhamentos

A terceira imagem mostra smbclient -L direcionado ao alvo com o usuário msfadmin. Após o prompt de senha, a saída apresenta compartilhamentos como print$, tmp, opt, IPC$, ADMIN$ e msfadmin, além do grupo WORKGROUP e do servidor METASPLOITABLE.

Conclusão sustentada: a execução retornou uma listagem de compartilhamentos no contexto da invocação com esse usuário. A senha digitada não é visível e a imagem isolada não permite determinar o mecanismo efetivo de autorização, inclusive eventual acesso convidado.

Não há evidência de teste de uma senha contra múltiplos usuários, portanto não se classifica essa atividade como password spraying. Também não há demonstração de leitura ou escrita no conteúdo dos compartilhamentos.

## 6. Aprendizados dos documentos

O autor relata familiarização com Medusa, reutilização de filtragem de dados e interesse em compreender parâmetros de autenticação. A captura FTP complementa o relato de filtragem ao mostrar um pipeline com grep; não se presume que seja o único procedimento mencionado no texto.

O segundo documento reúne comandos de navegação, arquivos, histórico e administração. A revisão técnica identifica ajustes importantes: cd opera sobre diretórios; grep é a grafia correta do filtro; gpasswd -d usuario grupo remove um membro do grupo; chown utiliza usuario:grupo. Esses ajustes não alteram os documentos originais preservados.

Comandos destrutivos e alterações administrativas presentes nas anotações exigem consulta à ajuda local e cuidado com perda de dados. Não são etapas obrigatórias deste projeto.

## 7. Matriz de resultados

| Atividade | Evidência disponível | Limite |
| --- | --- | --- |
| Medusa em FTP | Ferramenta reporta ACCOUNT FOUND/SUCCESS | Sem sessão FTP independente |
| Inspeção de formulário | POST e parâmetros visíveis | Sem comprovação de acesso ou automação |
| Listagem SMB | Compartilhamentos retornados | Sem comprovação de spraying ou acesso aos arquivos |
| Filtragem de saída | Pipeline visível na captura FTP | Sem comparação com a saída completa |
| Fundamentos Linux | Anotações originais | Sem validação individual de execução |
| Mitigação | Recomendações neste relatório | Sem teste antes/depois |

## 8. Recomendações defensivas

As orientações da OWASP incluem autenticação multifator onde aplicável, limitação de tentativas, bloqueios temporários cuidadosamente configurados e monitoramento de falhas. Mensagens de autenticação devem evitar revelar desnecessariamente a existência de contas.

As recomendações não representam controles já implementados neste laboratório. Uma etapa futura deve documentar um controle aplicado e comparar os resultados em condições equivalentes.

## 9. Limitações e continuidade

- Registrar versões, topologia e escopo autorizado.
- Preservar saídas completas e sanitizadas, não apenas linhas de sucesso.
- Validar separadamente o acesso FTP reportado pela ferramenta.
- Documentar o comportamento do formulário sem inferir sucesso a partir de códigos HTTP isolados.
- Diferenciar listagem de compartilhamentos, enumeração de usuários e password spraying.
- Revisar as imagens e os documentos antes de divulgação pública, especialmente se forem adicionados novos dados.

## 10. Conclusão

O material demonstra uma progressão entre estudo de comandos, observação de autenticação e análise de serviços. A evidência mais direta é o resultado FTP reportado pelo Medusa; as capturas web e SMB documentam atividades distintas, sem comprovar todos os objetivos propostos no enunciado.

A apresentação profissional do projeto depende de preservar essa distinção: registrar o que foi observado, explicar seus limites e indicar o que ainda precisa ser validado.

## Referências

- Documentos e capturas fornecidos pelo autor.
- OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- VirtualBox Virtual Networking: https://www.virtualbox.org/manual/ch06.html
