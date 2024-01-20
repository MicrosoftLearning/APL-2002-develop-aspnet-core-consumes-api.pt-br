---
lab:
  title: 'Exercício: renderizar respostas de API no Razor Pages do ASP.NET Core'
  module: 'Module: Render API responses in ASP.NET Core Razor Pages'
---

Neste exercício, você aprenderá a adicionar um código a um aplicativo Razor Pages no ASP.NET Core para renderizar resultados de operações HTTP. Esse código é adicionado aos arquivos *.cshtml*. O código que executa as operações nos arquivos *.cshtml.cs* foi concluído.

## Objetivos

Após concluir este exercício, você será capaz de:

* Implementar palavras-chave do Razor em um aplicativo
* Integrar um código C# com a sintaxe do Razor Pages

## Pré-requisitos

Para realizar o exercício, você precisará ter as seguintes ferramentas instaladas no sistema:

* [Visual Studio Code](https://code.visualstudio.com)
* [O SDK mais recente do .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* [A extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code

**Tempo estimado para concluir este exercício**: 30 minutos

## Cenário do exercício

Este exercício tem dois componentes:

* Um aplicativo Web que envia solicitações HTTP para uma API. O aplicativo é executado em `http://localhost:5010`
* Uma API que responde a solicitações HTTP. A API é executada em `http://localhost:5050`

![Decorativa](media/02-architecture.png)

## Baixar o código

Nesta seção, você baixará o código do aplicativo Web Fruit e da API Fruit. Você também executará a API Fruit localmente para que ela esteja disponível para o aplicativo Web.

### Tarefa 1: baixar e executar o código da API

1. Clique com o botão direito do mouse no link a seguir e selecione a opção **Salvar link como**. 

    * Código [do projeto FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Inicie o **Explorador de Arquivos** e vá até o local em que o arquivo foi salvo.

1. Descompacte o arquivo em sua própria pasta.

1. Abra o **Terminal do Windows** ou um **Prompt de Comando** e vá até o local em que você extraiu o código da API.

1. Em uma janela do **Terminal do Windows**, execute o seguinte comando do `dotnet`:

    ```
    dotnet run
    ```

1. Confira a seguir um exemplo do resultado gerado. Observe a linha `Now listening on: http://localhost:5050` na saída. Ela identifica o host e a porta da API.

    ```
    info: Microsoft.EntityFrameworkCore.Update[30100]
          Saved 3 entities to in-memory store.
    info: Microsoft.Hosting.Lifetime[14]
          Now listening on: http://localhost:5050
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: 
          <project location>
    ```

>**Observação:** deixe a API Fruit em execução durante todo o resto do exercício. 

### Tarefa 2: baixar e abrir o projeto de aplicativo Web

1. Clique com o botão direito do mouse no link a seguir e selecione a opção **Salvar link como**. 

    * [Código de projeto de renderização do app Web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-render.zip)

1. Inicie o **Explorador de Arquivos** e vá até o local em que o arquivo foi salvo.

1. Descompacte o arquivo em sua própria pasta.

1. Inicie o Visual Studio Code e selecione **Arquivo**. Em seguida, clique em **Abrir Pasta...** na barra de menus.

1. Vá até o local em que você descompactou os arquivos de projeto e selecione a pasta *FruitWebApp-render*.

1. A estrutura do projeto no painel **Explorer** deve ser semelhante à captura de tela a seguir. Se o painel **Explorer** não estiver visível, selecione **Visualizar** e, em seguida, selecione **Explorer** na barra de menus.

    ![Captura de tela mostrando a estrutura do projeto do aplicativo Web Fruit.](media/03-web-app-render-structure.png)

>**Observação:** reserve um tempo para revisar o código em cada um dos arquivos editados durante o exercício. O código é muito comentado e pode ajudar você a entender a base do código.

## Implementar código para renderizar dados na página `Index`

O aplicativo Web Fruit exibe os dados de exemplo da API na home page. Você precisa adicionar um código para iterar pelos dados de amostra retornados pela operação `GET` HTTP executada no arquivo code-behind.

### Tarefa 1: adicionar código para renderizar dados em uma tabela

1. Selecione o arquivo *Index.cshtml* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `@* Begin render API data code block *@` e `@* End render API data code block *@`.

    ```csharp
    <tbody>
        
        @*  The Razor keyword @foreach is used to iterate through the
            data returned to the data model from the HTTP operations. *@
        @foreach (var obj in Model.FruitModels)
        {
            <tr>
                @* Display the name of the fruit. *@
                <td width="50%">@obj.name</td>
                @*  The following if statment is a Razor code block that changes the color 
                    and icon of the available indicator in the page rendering. *@
                @{
                    if (@obj.instock)
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-check-circle" style="font-size: 1rem; color: green;"></i>&nbsp;Yes
                        </td>
                    }
                    else
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-dash-circle" style="font-size: 1rem; color:red;"></i>&nbsp;No
                        </td>
                    }
                }
                <td width="30%" class="text-center">
                    @*  The following div contains information to handle the edit and delete functions. *@
                    <div class="w-75 btn-group btn-group-sm" role="group" style="text-align:center">
                        @* Routes to the Edit page and passes the id of the record. *@
                        <a asp-page="Edit" asp-route-id="@obj.id" class="btn btn-primary  mx-2">
                            <i class="bi bi-pencil-square"></i> Edit
                        </a>
                        @* Routes to the Delete page and passes the id of the record. *@
                        <a asp-page="Delete" asp-route-id="@obj.id" class="btn btn-danger mx-2">
                            <i class="bi bi-trash"></i> Delete
                        </a>
                    </div>
                </td>
            </tr>
        }
    </tbody>
    ```

1. Salve as alterações em *Index.cshtml* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Verifique se a página Índice exibe os dados de exemplo da API.

    >**Observação:** as funções **Adicionar à lista**, **Editar** e **Excluir** não funcionarão até você adicionar um código para elas posteriormente neste exercício.

    >**Observação:** é possível ignorar com segurança o prompt abaixo se aparecer quando você executar o aplicativo.

    ![Captura de tela do prompt para instalar um certificado autoassinado.](media/install-cert.png)

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

## Implementar código para manipular a funcionalidade `Add to list`

As operações de adicionar, editar e excluir são tratadas em uma página *.cshtml* separada no projeto. Nesta seção, você adicionará um código para criar um formulário no aquivo *Add.cshtml* para habilitar a adição de dados à lista.

### Tarefa 1: adicionar código para criar o formulário de adição de dados

1. Selecione o arquivo *Add.cshtml* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `@* Begin render Add code block *@` e `@* End render Add code block *@`.

    ```csharp
    <form method="post">
        @*  The FruitModels.id is here so the full data model is represented on the page.
            The database behind the API will assign the id. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Add Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Empty text box for the name of the fruit to be added. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if it's available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the addition or return to the Index page if the Add is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Create</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Salve as alterações em *Add.cshtml* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Selecione **Adicionar à lista** na página.

1. Digite o nome de uma fruta que você deseja adicionar à lista e marque a caixa de seleção para indicar que ela está disponível.

1. Selecione **Criar** para adicionar a entrada à lista e você será roteado de volta para a página inicial. Verifique se a entrada foi adicionada à lista.

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

## Implementar código para manipular a funcionalidade `Edit`

Nesta seção, você adicionará um código para criar um formulário no arquivo *Edit.cshtml* para habilitar a edição de dados na lista.

### Tarefa 1: adicionar código ao formulário de edição

1. Selecione o arquivo *Edit.cshtml* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `@* Begin render Edit code block *@` e `@* End render Edit code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Edit Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the current name of the fruit in an editable text box. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the edit is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Update</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Salve as alterações em *Edit.cshtml* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Escolha um item na lista para alterar e selecione **Editar** nessa linha.

1. Edite o nome da fruta e marque a caixa de seleção para alterar o status de disponibilidade.

1. Selecione **Atualizar** para salvar suas alterações e você será encaminhado de volta para a página inicial. Verifique se a alteração é mostrada na lista.

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

## Implementar código para manipular a funcionalidade `Delete`

Nesta seção, você adicionará um código para criar um formulário no arquivo *Delete.cshtml*, habilitando a exclusão de dados da lista.

### Tarefa 1: adicionar código para o formulário de exclusão

1. Selecione o arquivo *Delete.cshtml* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `@* Begin render Delete code block *@` e `@* End render Delete code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be avaialable to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Delete Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in a non-editable text box. *@
                <input type="text" asp-for="FruitModels.name" disabled/>
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in a non-editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" disabled  />
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the delete is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Delete</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Salve as alterações em *Delete.cshtml* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Escolha um item na lista para excluir e selecione **Excluir** nessa linha.

1. Selecione **Excluir** e você será encaminhado para a página inicial. Verifique se o item excluído não aparece mais na lista.

Quando tudo estiver pronto para terminar o exercício:

* Feche o navegador ou a guia do navegador. No Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**. 

* Pare a API Fruit inserindo `Ctrl + C` no terminal em que está sendo executada.

## Revisão

Neste exercício você aprendeu a:

* Implementar palavras-chave do Razor em um aplicativo
* Integrar um código C# com a sintaxe do Razor Pages
