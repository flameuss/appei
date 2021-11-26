<p align="center"> 
   <img src="https://img.shields.io/badge/version-1.0-red.svg" />
  
  <a href="https://github.com/Flameuss">
    <img alt="Documentation" src="https://img.shields.io/badge/documentation-yes-brightgreen.svg" target="_blank" />
  </a>
</p>

<br>
### :pushpin: Índice

- [Sobre](#sobre-o-projeto)
- [Progresso](#progresso)
- [Como executar](#executar)
- [Criar o banco de dados](#banco-de-dados)
- [Codando](#coding)
- [Tecnologias](#tecnologia)
- [Desenvolvido por](#desenvolvido)

<br>
<a id="sobre-o-projeto"></a>

## 💻 Sobre o projeto

:memo: API para ser consumida por um formulario simples.

<br>
<a id="executar"></a>

## 🚀 Como executar o projeto
Antes de começar, você vai precisar ter instalado em sua máquina as seguintes ferramentas:
[XAMPP](https://www.apachefriends.org/pt_br/index.html) e o gerenciador de pacotes [Composer](https://getcomposer.org/).
Além disto é bom ter um editor para trabalhar com o código, como [VSCode](https://code.visualstudio.com/).
<br>

#### 🧭 Executando o programa

```bash
# Criar projeto com nome api dentro do seu localhost
$ composer create-project laravel/laravel api
# Acessar a pasta
cd api
# dar permissões 
chmod -R guo+w
#configurar o .env com as configurações de login colocar a rota ao banco de dados api. 

```
<br>

<a id="banco-de-dados"></a>

## 🏦 Banco de dados

```bash

# No Mysql workbench crie o banco de dados com o nome api
CREATE SCHEMA api DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci ;
# Selecione o banco de dados
use api;
# Crie a tabela  com o nome forms e insira as colunas 
CREATE TABLE forms (
  id bigint unsigned NOT NULL AUTO_INCREMENT,
  name varchar(255)  NOT NULL,
  dataNasc date NOT NULL,
  cpf varchar(11)  NOT NULL,
  celular varchar(20)  NOT NULL,
  email varchar(255)  NOT NULL,
  endereco varchar(255)  NOT NULL,
  observacao text(300)  NULL,
  created_at timestamp NULL DEFAULT NULL,
  updated_at timestamp NULL DEFAULT NULL,
  PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
# agora  voltamos ao programa
```
<br>

<a id="coding"></a>

## 👨‍💻 Codando

```bash

# voltando a nossa futura api, vamos apenas rodar o comando para criar as tabela migrations e  personal _access_tokens
# dentro do banco que ja tinhamos criado no passo acima.
php artisan migrate 
# agora com o banco de dados funcionando vamos criar o model e o controller
php artisan make:model Form -m
php artisan migrate
php artisan make:controller ApiController --resource
#
$ php artisan make:migration create_usuario_table --create=usuario
# Acesse a pasta /database/migrations e delete os arquivos de migração
#neste app nao vamos precisar criar nenhuma schema usando eloquent
# Definir controller
# Dentro de app/Http/Controllers/ApiController.php digite ou copie o codigo abaixo
<?php

namespace App\Http\Controllers;

use App\Models\Form as Form;
use App\Http\Resources\Form as FormResource;
use Illuminate\Http\Request;


class ApiController extends Controller
{
   
    public function index()
    {
        $forms = Form::paginate(15);
        return FormResource::collection($forms);
    }

    public function show($id)
    {
        $form = Form::findOrFail($id);
        return new FormResource($form);
    }

    public function store(Request $request)
    {
        $form = new Form;
        $form->name = $request->input('name');
        $form->dataNasc = $request->input('dataNasc');
        $form->cpf = $request->input('cpf');
        $form->celular = $request->input('celular');
        $form->email = $request->input('email');
        $form->endereco = $request->input('endereco');
        $form->observacao = $request->input('observacao');

        if ($form->save()) {
            return new FormResource($form);
        }
    }
      

    public function update(Request $request)
    {
        $form = Form::findOrFail($request->id);
        $form->name = $request->input('name');
        $form->dataNasc = $request->input('dataNasc');
        $form->cpf = $request->input('cpf');
        $form->celular = $request->input('celular');
        $form->email = $request->input('email');
        $form->endereco = $request->input('endereco');
        $form->observacao = $request->input('observacao');
        // return response()->json([
        //     "message" => "registro de cliente criado"
        // ], 201);

        if ($form->save()) {
            return new FormResource($form);
        }
    }

    public function destroy($id)
    {
        $form = Form::findOrFail($id);
        if ($form->delete()) {
            return new FormResource($form);
        }
    }
}

# Criar Rotas

# Dentro da pasta /routes/api.php digite ou copie este codigo
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ApiController;


Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
    

});
// Listar usuarios
Route::get('form', [ApiController::class, 'index']);
// Listar usuários especifico
Route::get('form/{id}', [ApiController::class, 'show']);
// Criar novo usuario
Route::post('form', [ApiController::class, 'store']);
// Atualizar usuarios
Route::put('form/{id}', [ApiController::class, 'update']);
// Atualizar usuarios
Route::delete('form/{id}', [ApiController::class, 'destroy']);

# Criar e definir resource
php artisan make:resource Form
# Acesse o arquivo app/Http/Resources/Form.php e digite ou copie este codigo
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class Form extends JsonResource
{
    
    public function toArray($request)
    {
        return 
        [
            'id'=>$this->id,
            'name'=>$this->name,
            'dataNasc'=>$this->dataNasc,
            'cpf'=>$this->cpf, 
            'celular'=>$this->celular, 
            'email'=>$this->email, 
            'endereco'=>$this->endereco, 
            'observacao'=>$this->observacao
        ];
    }
}

# Agora é só testar no Insomnia usando os seguintes URLs
# Para cadastrar (POST)
 http://localhost/api/public/api/form
# Para Listar (GET)
 http://localhost/api/public/api/form
# Para Listar especifico (GET)
 http://localhost/api/public/api/form/{ID}
# Para editar (PUT)
 http://localhost/api/public/api/form/{ID}
# Para deletar (DELETE)
 http://localhost/api/public/api/form/{ID}
```
