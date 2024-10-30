# README - Deploy Logan(Produção)

Este guia fornece instruções detalhadas sobre como realizar um deploy do LOGAN em produção. Certifique-se de seguir cada etapa com atenção para garantir um processo de deploy seguro e bem sucedido.

## Passos para o Deploy

1. **Versionamento do LOGAN é baseado na data da versão:**
- ![Captura de tela de 2024-10-16 15-30-15](https://github.com/user-attachments/assets/dae70207-a38e-4d38-ad97-f2e932c3ed62)
   - Ou seja, a nova versão será a v2024.09.27.

2. **Criar o Pull Request da develop para master(padrão).**    

3. **Atualizar a versão do template.xhtml:**
- ![Captura de tela de 2024-10-16 15-33-23](https://github.com/user-attachments/assets/5872c649-0b55-4925-8a1d-1b29b820e10a)

4. **Mergear o PR e criar a TAG:**
- ![Captura de tela de 2024-10-16 15-36-24](https://github.com/user-attachments/assets/472d915d-1ef3-4e96-b29d-fe0b7ca1c908)

5. **Atualizar o GIT dos projetos do LOGAN, para puxar as alterações que vão entrar.**
 

6. **Na classe Entity do projeto CTFL-Support, comentar a linha 8 e descomentar a linha 14 e rodar o LOGAN:**
- ![image (1)](https://github.com/user-attachments/assets/a6b92003-61a7-476c-b0b1-d68d7622e51a)
- Dar o CLEAN em todos os projetos;
- Fazer o BUILD na seguinte ordem:
  - Support, Dominio, Lib-Mail, Aviso e WebApp; 


7. **No FileZilla, conectar no FTP com o usuário suporte(diferente do usuário para as API's):**
- ![image (2)](https://github.com/user-attachments/assets/2bc25154-ecbd-490e-889e-27d6f8dde0b8)


8. **Mover o WAR na pasta Logan/Oficial:**
- ![image (3)](https://github.com/user-attachments/assets/babd18b9-105b-460e-9753-a06b9fe41832)
  - Renomear o WAR mais recente e apagar o mais antigo(padrão das API's).    

9. **Logar na máquina base do LOGAN**
    - `ssh -i ~/Amazon/webservice.pem ubuntu@54.226.132.182`

10. **Na pasta deploy do JBOSS, apagar o WAR e gerar o novo :**
    - ![image (4)](https://github.com/user-attachments/assets/93ef896a-a6ba-4897-bbc2-71502649bcea)


11. **Aguardar o LOGAN subir novamente e verificar no log e máquina base, para confirmar se está tudo certo.**
    - ![image (5)](https://github.com/user-attachments/assets/4d5eaeed-7384-4292-8582-838b6aabad53)
      - http://54.226.132.182:8084/CTFLLogan-webapp
      - Observar o começo da tag `<head>` e confirmar se versão está correta.


12. **AWS - Criação de Imagem e Atualização do Launch Templates:**
    -  ![image (6)](https://github.com/user-attachments/assets/4f6fb81f-f86f-4316-b3c5-e51c6e171143)

    - Crie uma imagem da instância atualizada na AWS:
        -  `Images` -> `AMIs`;
        -  Digite a API na busca e filtre a data de criação mais recente e selecione;
        -  Na aba `Detais`, copie o `AMI name` e altere para a data e versão atual;
        -  Ex:
            - AMI ANTERIOR:
                > *ami-logan-14-08-24-v2024.08.14*
            - AMI NOVA:
                > *ami-logan-27-09-24-v2024.09.27*
    - Em EC2:
        - `Instances` -> procure a API desejada;
        - Clique na instância e em `Actions`, selecione `Image and templates` -> `Create image`.
    - Criando a imagem:
    	- `Image name` -> cole o nome da AMI NOVA criada.
     	    > Ex: *ami-logan-27-09-24-v2024.09.27*.
		- `Image description - optional` -> cole novamente a *ami-logan-27-09-24-v2024.09.27*;
		- IMPORTANTE!!!:
 	  	    > `Reboot Instance` -> *DESMARCAR O CHECK E VERIFICAR SE O "Delete on termination" TAMBÉM ESTÁ MARCADO* Não é necessário reinicializar a instância.
		- `Add new tag` -> Em `Key` insira `"Name"` e cole a AMI NOVA, ex: *ami-logan-27-09-24-v2024.09.27*;
		- Verifique os campos novamente e clique em `Create image`, vai demorar um pouco até que a imagem seja criada e o status fique `"Available"` na aba AMIs.   
    - Com a imagem criada:
        - `Instances` -> `Lauch Templates`, procure a API desejada e selecione;
		- Em `Launch template version details` -> `Actions` -> `Modify template (Create new version)`;
		- No campo `Template version description` -> Cole a AMI NOVA, porém sem o "ami-".
            > Ex: *logan-27-09-24-v2024.09.27*.
		- Em `Application and OS Images (Amazon Machine Image)` -> cole a AMI NOVA e *ENTER* para buscar;
            > Ex: *ami-logan-27-09-24-v2024.09.27*        
		- Agora em `My AMIs`, clique em `Select` na imagem buscada e em seguida `Create template version`;
		- Novamente em `Launch template version details` -> `Actions` -> `Set default version`, selecione a versão criada e clique em `Set as default version`. 
    
14. **Não é necessário forçar a troca da atualização no LOGAN**

15. **Observação:**
    - Certifique-se de seguir os passos com cuidado e sempre faça testes antes de aplicar em ambientes de produção. Este guia assume familiaridade com as ferramentas e conceitos mencionados.

