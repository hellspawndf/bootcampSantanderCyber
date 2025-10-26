Atividades realizadas

Nota ao avaliador: eu tive dificuldade em compartilhar a área de trabalho entre o Kali e meu PC host. Sendo assim, eu não fiz uma lista de comandos no README.md (o criei no host). Mas todas as capturas contém os comandos executados.

Mais uma nota: passei um dia inteiro tentando mandar as imagens para o gitub. Sempre dá erro ao visualizar e elas ficam quebradas. Mas, ao baixar o zip com o repositório está tudo certo. Não sei mais o que fazer!

1) Preparação do ambiente
    
    1.1) Baixei a VM no site https://www.kali.org/
        1.1.1) Link utilizado: https://cdimage.kali.org/kali-2025.3/kali-linux-2025.3-vmware-amd64.7z
    1.2) Subi a VM no VMware
    1.3) Atualizei a VM usando:
        apt update
        apt upgrade
        apt dist-upgrade
        1.3.1) Tive que fazer isto algumas vezes até finalizar. As duas primeiras capturas mostram o resultado final
    1.4) O mouse não funcionou. Atualizei usando esta dica: https://www.reddit.com/r/Kalilinux/comments/1o0mxon/cursor_invisible_after_updates/
    1.5) Baixei o metasploitable2 em: https://sourceforge.net/projects/metasploitable/
    1.6) Subi a VM no VMware
    1.7) Mesmo botando as VMs em host-only, o Metasploitable2 não pegou IP, então tive que colocar na mão
    1.8) Identifiquei os seguintes IPs, conforme as capturas:
        - Kali:            192.168.18.128
        - Metasploitable2: 192.168.18.129

 
2) Executei o ataque ao serviço ftp, criando a lista de usuários e senhas (incluí outras senhas comuns no arquivo), usando o medusa, conforme descrito na aula. Inseri as capturas que mostram o processo.
    2.1) Apenas por curiosidade, fiz um acesso anônimo ao ftp para testar (usuário anônimo e um e-mail qualquer como senha. Claro que ele tem acesso restrito ao que foi permitido pelo admin, ao contrário do login com o usuário msfadmin, que tem acesso à raiz do diretório ftp, entre outros.

    Algumas recomendações de mitigação:
    - não usar FTP :). É um protocolo obsoleto hoje em dia! Usar SFTP ou HTTPS como substituto.
    - desativar login anônimo
    - políticas de senha (comprimento mínimo, complexidade, rotação, validade) e evitar contas compartilhadas.
    - bloquear conta após N tentativas falhas (ex.: 5-10)
    - limitar número de tentativas por IP
    - gerar alertas em tentativas de autenticação anômalas (no próprio sistema ou usando um SIEM por exemplo)
    - reduzir exposição de serviços sensíveis em redes públicas
    - ter um serviço de monitoramento da internet, deep e dark web contratado (DRP - Digital Risk Protection por exemplo)


3) Executei o ataque a formulários de login, conforme descrito na aula. Inseri as capturas que mostram o progresso desta prática.
    3.1) Eu não criei as wordlists novamente porque eram as mesmas do item anterior (eu já havia feito listas um pouco maiores do que as sugeridas).
    3.2) Como eu tinha inserido algumas senhas além das fornecidas pela Professora (como 1234, por exemplo) na wordlist de senhas, em um primeiro momento entendi que havia tido alguns eventos de sucesso a mais que os mostrados na aula. Depois de pesquisar no fórum entendi que é uma pequena incorreção na aula, pois o medusa não consegue fazer esse tipo de ataque. 
    Usando a dica do fórum, usei o Hydra, que me mostrou que das 44 combinações possíveis (4 usuários e 11 senhas), 42 funcionaram (msfadmin:rockyou e msfadmin:monkey não funcionaram). Ocorre que, ao testar várias, combinações, a maioria não funcionou :-(. Acabei usando admin:password, a única que funcionou entre as que testei.
    Continuei pesquisando mas não consegui entender bem porque houve tantos falsos positivos. Ainda tentei mudar os argumentos do hydra (threads paralelas, aumentei verbosidade, etc.), conforme a captura, mas, por exemplo, msfadmin:msfadmin de fato não funciona.
    Ainda fiz um teste com o curl para entender o que acontece com uma senha aleatória, como na captura. Como o dvwa dá mensagem 302 (Found) e não Login Failed, acho que está aí o problema. 
    Por fim, tentei usar o wfuzz (como as capturas mostram), mas não achei uma sintaxe que funcionasse.

    Algumas recomendações de mitigação:
    - não usar http, que é inseguro :)
    -  políticas de senha (comprimento mínimo, complexidade, rotação, validade) e evitar contas compartilhadas.
    - bloquear conta após N tentativas falhas (ex.: 5-10)
    - limitar número de tentativas por IP
    - gerar alertas em tentativas de autenticação anômalas (no próprio sistema ou usando um SIEM por exemplo)
    - reduzir exposição de serviços sensíveis em redes públicas
    - ter um serviço de monitoramento da internet, deep e dark web contratado (DRP - Digital Risk Protection por exemplo) para ver se as senhas vazaram. Usar o haveibeenpwned também é uma alternativa
    - CAPTCHA/antibot, verificação comportamental ou desafios progressivos
    - proteção contra CSRF
    - segurança de sessão (cookies seguros, sessão curta, etc.)
    - usar um WAF (Web Application Firewall)



4) Antes de criar a wordlist de usuários, pesquisei nomes de usuário mais comuns ao se fazer o exploit do smb. Sendo assim, adicionei alguns usuários a mais do que os levantados na aula (como admin, administrador, administrator, etc.) como pode ser visto na captura.
    Já para a wordlist de senhas, eu copiei o pass.txt anterior e "appendei" (acrescentei) o Welcome123 no final, como pode ser visto nas capturas.
    Eu executei o medusa e visualizei o teste com sucesso. Entretanto, tinha muitas linhas. Para facilitar, executei a saída com um grep para filtrar apenas o que tinha "FOUND". Depois que vi que deveria ter criado o usuário krbtgt (Kerberos TGT - o ticket granting ticket do Kerberos) e não krntgt (foi um erro de digitação)

    Algumas recomendações de mitigação:
    - cuidado com a versão do SMB. A V1, por exemplo, é obsoleta e insegura
    - fazer tierização no caso de uso de AD
    - usar ferramentas para rotacionar senhas de contas de serviço
    - segmentar a rede para evitar ataques laterais
    - políticas de senha (comprimento mínimo, complexidade, rotação, validade) e evitar contas compartilhadas
    - desativar compartilhamentos desnecessários
    - permissões e compartilhamentos só para quem precisa (menor privilégio)
    - gerenciamento de vulnerabilidades (para identificar versões de protocolos vulneráveis)
    - bloquear conta após N tentativas falhas (ex.: 5-10)
    - limitar número de tentativas por IP
    - gerar alertas em tentativas de autenticação anômalas (no próprio sistema ou usando um SIEM por exemplo)
    - reduzir exposição de serviços sensíveis em redes públicas
    - ter um serviço de monitoramento da internet, deep e dark web contratado (DRP - Digital Risk Protection por exemplo) para ver se as senhas vazaram. Usar o haveibeenpwned também é uma alternativa
  