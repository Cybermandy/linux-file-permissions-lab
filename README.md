# 🔐 Linux File Permissions Management
### Gerenciamento de Permissões de Arquivos no Linux

Laboratório prático de **cybersecurity**: auditoria e correção de **file and directory permissions** em um ambiente Linux, aplicando o **Principle of Least Privilege** (princípio do menor privilégio).

> Atividade: *Manage Authorization* (Gerenciar autorização), laboratório prático do Google.

**Skills:** `Linux` · `Bash` · `File Permissions` · `Access Control` · `Least Privilege` · `Security Auditing`

---

## 📋 Scenario (Cenário)

A equipe de pesquisa de uma organização precisava atualizar as permissões de arquivos e diretórios no diretório `projects`. As permissões atuais não correspondiam ao nível de **authorization** (autorização) que cada tipo de proprietário deveria ter.

O objetivo foi **verificar as permissões existentes e corrigi-las**, garantindo que cada **user**, **group** e **others** tivesse acesso apenas ao que é necessário.

**Environment (Ambiente):**
- User: `researcher2`
- Group: `research_team`
- Directory: `/home/researcher2/projects`

---

## 🛠️ Tools & Concepts (Ferramentas e conceitos)

- Linux / Bash
- `ls -l` e `ls -la`: listagem de permissões, incluindo **hidden files** (arquivos ocultos)
- `chmod`: alteração de permissões
- **Principle of Least Privilege** (princípio do menor privilégio)
- **Access Control** por **user** (`u`), **group** (`g`) e **others** (`o`)

---

## 🔍 Reading Permissions (Como ler as permissões)

As permissões aparecem como uma sequência de 10 caracteres. Exemplo: `drwxrwxrwx`

| Posição | Significado |
|---|---|
| 1º | Type (tipo): `d` = directory, `-` = file |
| 2º a 4º | **User** permissions (proprietário) |
| 5º a 7º | **Group** permissions (grupo) |
| 8º a 10º | **Others** permissions (outros) |

`r` = **read** (leitura) · `w` = **write** (gravação) · `x` = **execute** (execução) · `-` = sem permissão

---

## 1. Initial Check (Verificação inicial)

Primeiro, entrei no diretório `projects` e listei o conteúdo com `ls -la` para ver também os **hidden files** (que começam com `.`).

```bash
cd /home/researcher2/projects
ls -la
```

```
drwxr-xr-x 3 researcher2 research_team 4096 Oct 17 14:26 .
drwxr-xr-x 3 researcher2 research_team 4096 Oct 17 16:16 ..
-rw--w---- 1 researcher2 research_team   46 Oct 17 14:26 .project_x.txt
drwx--x--- 2 researcher2 research_team 4096 Oct 17 14:26 drafts
-rw-rw-rw- 1 researcher2 research_team   46 Oct 17 14:26 project_k.txt
-rw-r----- 1 researcher2 research_team   46 Oct 17 14:26 project_m.txt
-rw-rw-r-- 1 researcher2 research_team   46 Oct 17 14:26 project_r.txt
-rw-rw-r-- 1 researcher2 research_team   46 Oct 17 14:26 project_t.txt
```

![Initial listing](<img width="1600" height="646" alt="fafea8b7-ba8e-4f4d-b789-70fe6d66b5cd" src="https://github.com/user-attachments/assets/988e5f71-2339-4c9d-88fc-9026d77bf290" />
)

---

## 2. Remove Write Access for Others: `project_k.txt`
### Remover gravação de "outros"

**Issue (problema):** o arquivo podia ser alterado por qualquer usuário do sistema (**others** com **write**).
**Policy (política):** **others** não devem ter permissão de gravação.

```bash
chmod o-w project_k.txt
```

| Before (antes) | After (depois) |
|---|---|
| `-rw-rw-rw-` | `-rw-rw-r--` |

![project_k.txt](<img width="902" height="763" alt="7fda4aa9-e498-4607-9503-2e0eb73d2b0d" src="https://github.com/user-attachments/assets/cb29d708-6b5a-4b16-8d72-a05a36c2b065" />
)

---

## 3. Restrict a Confidential File: `project_m.txt`
### Restringir arquivo confidencial

**Issue (problema):** o **group** tinha permissão de **read** em um arquivo restrito.
**Policy (política):** apenas o **user** proprietário pode ler e gravar.

```bash
chmod g-r project_m.txt
```

| Before (antes) | After (depois) |
|---|---|
| `-rw-r-----` | `-rw-------` |

---

## 4. Protect an Archived Hidden File: `.project_x.txt`
### Proteger arquivo oculto arquivado

**Issue (problema):** o arquivo foi arquivado e não deveria ser alterado por ninguém, mas **user** e **group** tinham **write**. O **group** também não conseguia ler o arquivo.
**Policy (política):** **user** e **group** podem apenas ler (**read-only**); ninguém pode gravar.

```bash
chmod u-w,g-w,g+r .project_x.txt
```

| Before (antes) | After (depois) |
|---|---|
| `-rw--w----` | `-r--r-----` |

![.project_x.txt](<img width="902" height="763" alt="project_x txt" src="https://github.com/user-attachments/assets/39a2134a-9186-4701-abcc-3a953a147d86" />
)

---

## 5. Restrict Directory Access: `drafts`
### Restringir acesso ao diretório

**Issue (problema):** o **group** tinha **execute** no diretório, o que permite entrar nele e acessar seu conteúdo.
**Policy (política):** apenas `researcher2` pode acessar o diretório `drafts`.

```bash
chmod g-x drafts
```

| Before (antes) | After (depois) |
|---|---|
| `drwx--x---` | `drwx------` |

---

## ✅ Final Result (Resultado final)

```
-r--r----- 1 researcher2 research_team   46 Oct 17 14:26 .project_x.txt
drwx------ 2 researcher2 research_team 4096 Oct 17 14:26 drafts
-rw-rw-r-- 1 researcher2 research_team   46 Oct 17 14:26 project_k.txt
-rw------- 1 researcher2 research_team   46 Oct 17 14:26 project_m.txt
-rw-rw-r-- 1 researcher2 research_team   46 Oct 17 14:26 project_r.txt
-rw-rw-r-- 1 researcher2 research_team   46 Oct 17 14:26 project_t.txt
```

| Item | Before | After | Command |
|---|---|---|---|
| `project_k.txt` | `-rw-rw-rw-` | `-rw-rw-r--` | `chmod o-w` |
| `project_m.txt` | `-rw-r-----` | `-rw-------` | `chmod g-r` |
| `.project_x.txt` | `-rw--w----` | `-r--r-----` | `chmod u-w,g-w,g+r` |
| `drafts/` | `drwx--x---` | `drwx------` | `chmod g-x` |

![Final result](<img width="886" height="827" alt="FINAL RESULT" src="https://github.com/user-attachments/assets/e17e898e-7410-4e2c-8e59-93a868a1a250" />
)

---

## 💡 Key Takeaways (Aprendizados)

- **`ls -la` é essencial em uma security audit**: **hidden files** como `.project_x.txt` não aparecem com `ls -l` e podem ter permissões incorretas.
- **Execute em diretório não é "rodar um programa"**: o `x` em um **directory** controla quem pode entrar nele e acessar seu conteúdo.
- **Least Privilege na prática**: cada **user**, **group** e **others** deve ter só o acesso necessário. Isso reduz o risco de **unauthorized modification** (alteração indevida), **data leakage** (vazamento de dados) e uso malicioso de arquivos.
- **Symbolic mode do `chmod`** (`u`, `g`, `o` com `+` e `-`) permite ajustes precisos sem alterar o restante das permissões.

---

## 👩‍💻 Author (Autora)

**Amanndha De Sena Miguel**
