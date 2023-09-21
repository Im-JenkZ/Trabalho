from fastapi import FastAPI, Depends, HTTPException, status
from pydantic import BaseModel
from typing import List, Optional
from passlib.context import CryptContext
from datetime import datetime, timedelta
from jose import JWTError, jwt
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from fastapi.openapi.models import OAuthFlows as OAuthFlowsModel
from fastapi.openapi.models import OAuthFlowPassword as OAuthFlowPasswordModel

app = FastAPI()

# Chave secreta usada para assinar e verificar tokens JWT
SECRET_KEY = "chave"

# Configurações para geração de tokens JWT
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30  # Tempo de expiração do token em minutos

# Exemplo de dados de usuário (substitua por um banco de dados real)
fake_users_db = {
    "usuario": {
        "id": 1,
        "username": "usuario",
        "password":
        "$2b$12$Sv7EKMJ./oZdJnyC2oMwPej/rS7VgjJH8p.g/Sz5fNYtGto08f8nu",  # senha: password
    }
}

# Configuração para autenticação JWT
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


# Função para criar tokens JWT
def create_access_token(data: dict, expires_delta: timedelta = None):
  to_encode = data.copy()
  if expires_delta:
    expire = datetime.utcnow() + expires_delta
  else:
    expire = datetime.utcnow() + timedelta(minutes=15)
  to_encode.update({"exp": expire})
  encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
  return encoded_jwt


# Rota de login para autenticar usuários e gerar tokens JWT
@app.post("/token", response_model=dict)
async def login_for_access_token(
    form_data: OAuth2PasswordRequestForm = Depends()):
  user = fake_users_db.get(form_data.username)
  if user is None or not verify_password(form_data.password, user["password"]):
    raise HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Credenciais inválidas",
        headers={"WWW-Authenticate": "Bearer"},
    )

  access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
  access_token = create_access_token(data={"sub": form_data.username},
                                     expires_delta=access_token_expires)
  return {"access_token": access_token, "token_type": "bearer"}


# Rota para listar todos os itens (exemplo)
@app.get("/listar_itens/", response_model=List[Item])
async def listar_itens(
    usuario_atual: DadosToken = Depends(obter_usuario_atual)):

  itens = [
      {
          "nome": "Item 1",
          "descricao": "Descrição do Item 1"
      },
      {
          "nome": "Item 2",
          "descricao": "Descrição do Item 2"
      },
  ]
  return itens


class Token(BaseModel):
  access_token: str
  token_type: str


class TokenData(BaseModel):
  username: str = None


class TipoUsuario(BaseModel):
  id: int
  nome: str


class Endereco(BaseModel):
  id: int
  rua: str
  bairro: str
  cep: str


class Usuario(BaseModel):
  id: int
  id_tipo_usuario: int
  id_endereco: int
  nome: str
  genero: str
  email: str


class HistoricoEmprestimo(BaseModel):
  id: int
  data_entrada: str
  data_saida: str
  status: str
  id_usuario: int
  id_obra: int


class Editora(BaseModel):
  id: int
  nome: str
  endereco: Endereco


class Autor(BaseModel):
  id: int
  nome: str
  nacionalidade: str


class Obras(BaseModel):
  id: int
  id_periodico: int
  id_editora: int
  id_autores: List[int]
  titulo: str
  midia: str
  lingua: str
  ano: int


# Exemplos de objetos
tipo_usuario_example = TipoUsuario(id=1, nome="max")
endereco_example = Endereco(id=1,
                            rua="Rua Principal",
                            bairro="Centro",
                            cep="12345-678")

usuario_example = Usuario(id=1,
                          id_tipo_usuario=1,
                          id_endereco=1,
                          nome="elielton",
                          genero="Masculino",
                          email="joao@email.com")

historico_emprestimo_example = HistoricoEmprestimo(id=1,
                                                   data_entrada="2023-09-16",
                                                   data_saida="2023-09-30",
                                                   status="Em andamento",
                                                   id_usuario=1,
                                                   id_obra=1)

editora_example = Editora(id=1, nome="Editora XYZ", endereco=endereco_example)
autor_example = Autor(id=1, nome="Autor A", nacionalidade="Brasileiro")
obra_example = Obras(id=1,
                     id_periodico=1,
                     id_editora=1,
                     id_autores=[1],
                     titulo="Livro 1",
                     midia="Livro",
                     lingua="Português",
                     ano=2023)

# Crie listas ou dicionários para armazenar esses exemplos de objetos.
tipo_usuarios = [tipo_usuario_example]
enderecos = [endereco_example]
usuarios = [usuario_example]
historico_emprestimos = [historico_emprestimo_example]
editoras = [editora_example]
autores = [autor_example]
obras = [obra_example]


@app.get("/tipo_usuario/{tipo_usuario_id}")
async def get_tipo_usuario(tipo_usuario_id: int):
  for tipo_usuario in tipo_usuarios:
    if tipo_usuario.id == tipo_usuario_id:
      return tipo_usuario
  return None


@app.get("/endereco/{endereco_id}")
async def get_endereco(endereco_id: int):
  for endereco in enderecos:
    if endereco.id == endereco_id:
      return endereco
  return None


@app.get("/usuario/{usuario_id}")
async def get_usuario(usuario_id: int):
  for usuario in usuarios:
    if usuario.id == usuario_id:
      return usuario
  return None


@app.get("/historico_emprestimo/{historico_id}")
async def get_historico_emprestimo(historico_id: int):
  for historico in historico_emprestimos:
    if historico.id == historico_id:
      return historico
  return None


@app.get("/editora/{editora_id}")
async def get_editora(editora_id: int):
  for editora in editoras:
    if editora.id == editora_id:
      return editora
  return None


@app.get("/autor/{autor_id}")
async def get_autor(autor_id: int):
  for autor in autores:
    if autor.id == autor_id:
      return autor
  return None


@app.get("/obra/{obra_id}")
async def get_obra(obra_id: int):
  for obra in obras:
    if obra.id == obra_id:
      return obra
  return None


# uvicorn main:app --reload
# http://127.0.0.1:8000
# http://127.0.0.1:8000
