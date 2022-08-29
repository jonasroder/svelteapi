<script>
  import { each } from "svelte/internal";

  let showModal = false;
  let veiculos = [];
  let dadosVeiculos = {
    id: null,
    nome: "",
    marca: "",
    ano: "",
    valorvenda: "",
  };

  let listarVeiculos = () => {
    fetch(`http://localhost/CRUD_AvaliaSistemas/veiculos/read`)
      .then((resp) => resp.json())
      .then((dadosResposta) => {
        veiculos = dadosResposta;
        dadosVeiculos = {
          id: null,
          nome: "",
          marca: "",
          ano: "",
          valorvenda: "",
        };

        console.log(veiculos);
      })
      .catch(console.error());
  };
  listarVeiculos();

  let salvarVeiculo = () => {
    const novoVeiculo = {
      nome: dadosVeiculos.nome,
      marca: dadosVeiculos.marca,
      ano: dadosVeiculos.ano,
      valorvenda: dadosVeiculos.valorvenda,
    };

    fetch(`http://localhost/CRUD_AvaliaSistemas/veiculos/create`, {
      method: "POST",
      body: JSON.stringify(novoVeiculo),
    })
      .then((resp) => resp.json())
      .then((dadosResposta) => {
        console.log(novoVeiculo);
        listarVeiculos();
      })
      .catch(console.error());
  };

  let updateVeiculo = (idVeidulo) => {
	debugger
	showModal = true
  }
</script>

<main>
  <header>
    <nav class="navbar navbar-expand-lg bg-light">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">Avalia</a>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon" />
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item" />
          </ul>
          <form
            class="d-flex pull-right"
            role="search"
            onsubmit="event.preventDefault(); buscar();"
          >
            <input
              id="busca"
              class="form-control me-2"
              type="search"
              placeholder="Pesquisar"
              aria-label="Pesquisar"
            />
            <button class="btn btn-outline-success" type="submit">Buscar</button
            >
          </form>
        </div>
      </div>
    </nav>
  </header>

  <div class="container mt-5">
    <div class="title">
      <div class="row d-flex">
        <div class="col-sm-6">
          <h2>Listar <b>Carros</b></h2>
        </div>
        <div class="col-sm-6" style="text-align: right">
          <button
            id="apagarcarros"
            type="button"
            class="btn btn-danger"
            style="display:none"
            onclick="deleteMany()"
          >
            Apagar carros
          </button>

          <button
            type="button"
            class="btn btn-success"
            data-bs-toggle="modal"
            data-bs-target="#addcarro"
            onclick="$('#adicionar')[0].reset()"
          >
            Adicionar carro
          </button>
        </div>
      </div>
    </div>

    <div id="carros" class="list-group mx-0 w-auto mt-5">
      {#each veiculos as veiculo}
        <div
          on:click={updateVeiculo}
          class="list-group-item d-flex gap-2 pointer"
          id="carro{veiculo.idveiculos}"
        >
          <input
            class="form-check-input flex-shrink-0"
            type="checkbox"
            value={veiculo.idveiculos}
            onclick="check()"
          />
          <div>
            <span
              >{veiculo.nome} / {veiculo.ano}
              <small class="d-block text-muted">{veiculo.marca}</small></span
            >
          </div>
          <span class="price bold"
            ><b
              >R$ {parseInt(veiculo.valorvenda).toLocaleString("pt-BR")}
            </b></span
          >
        </div>
      {/each}
    </div>
  </div>

  <!-- Add Modal HTML -->
  <div class="modal" tabindex="-1" id="addcarro"  on:close="{() => showModal = false}">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Adicionar veículo</h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          />
        </div>
        <div class="modal-body">
          <div>
            <form id="adicionar">
              <div class="mb-3">
                <label class="form-label">Modelo</label>
                <input
                  bind:value={dadosVeiculos.nome}
                  type="text"
                  class="form-control"
                  required
                />
              </div>

              <div class="mb-3">
                <label class="form-label">Marca</label>
                <input
                  bind:value={dadosVeiculos.marca}
                  type="text"
                  class="form-control"
                  required
                />
              </div>

              <div class="mb-3">
                <label class="form-label">Ano</label>
                <input
                  bind:value={dadosVeiculos.ano}
                  type="text"
                  class="form-control"
                  required
                />
              </div>

              <div class="mt-3">
                <label class="form-label">Valor de venda</label>
                <div class="input-group mt-1 mb-3">
                  <span class="input-group-text">R$</span>
                  <input
                    bind:value={dadosVeiculos.valorvenda}
                    class="form-control"
                    aria-label="Digite o valor de venda"
                    type="number"
                  />
                  <span class="input-group-text">.00</span>
                </div>
              </div>
            </form>
          </div>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-secondary"
            data-bs-dismiss="modal">Fechar</button
          >
          <button
            type="button"
            class="btn btn-danger"
            data-bs-dismiss="modal"
            on:click|preventDefault={salvarVeiculo}>Salvar</button
          >
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  .form-check-input,
  .price {
    align-self: center;
    padding: 8px;
    margin-right: 8px;
  }

  .price {
    right: 30px;
    position: absolute;
  }

  .list-group-item:hover {
    background-color: #e9e9e9;
  }

  .pointer:hover {
    cursor: pointer;
  }
</style>
