# esquema_logico_bd
Desafio DIO

**Esquema Lógico do Banco de Dados para Oficina**
O esquema lógico do banco de dados para uma oficina pode ser representado da seguinte forma:

**Tabelas Principais:**
Cliente (ClienteID, Nome, Endereço, Telefone)

Chave Primária: ClienteID
Veiculo (VeiculoID, Placa, Modelo, AnoFabricacao, ClienteID)

Chave Primária: VeiculoID
Chave Estrangeira: ClienteID (referenciando Cliente)
Servico (ServicoID, Nome, Preco)

Chave Primária: ServicoID
OrdemServico (OrdemServicoID, DataAbertura, DataFechamento, VeiculoID, FuncionarioID)

Chave Primária: OrdemServicoID
Chave Estrangeira: VeiculoID (referenciando Veiculo)
Chave Estrangeira: FuncionarioID (referenciando Funcionario)
ItemServico (ItemServicoID, OrdemServicoID, ServicoID)

Chave Primária: ItemServicoID
Chave Estrangeira: OrdemServicoID (referenciando OrdemServico)
Chave Estrangeira: ServicoID (referenciando Servico)
Funcionario (FuncionarioID, Nome, Cargo)

Chave Primária: FuncionarioID
Tabelas de Relacionamento:

Peca (PecaID, Nome, Preco)

Chave Primária: PecaID
ItemPeca (ItemPecaID, OrdemServicoID, PecaID, Quantidade)

Chave Primária: ItemPecaID
Chave Estrangeira: OrdemServicoID (referenciando OrdemServico)
Chave Estrangeira: PecaID (referenciando Peca)
Agora que temos o esquema lógico, vou criar algumas consultas SQL com base nas diretrizes fornecidas.

**Consultas SQL**
**1.** Recuperações simples com SELECT Statement:
Recuperar todos os clientes: SELECT * FROM Cliente;
Recuperar todos os veículos: SELECT * FROM Veiculo;

**2.** Filtros com WHERE Statement:
Recuperar todos os veículos de um cliente específico: SELECT * FROM Veiculo WHERE ClienteID = 1;
Recuperar todas as ordens de serviço abertas: SELECT * FROM OrdemServico WHERE DataFechamento IS NULL;

**3.** Crie expressões para gerar atributos derivados:
Calcular o total a ser pago em uma ordem de serviço, somando o preço dos serviços e das peças:
   SELECT os.OrdemServicoID, SUM(s.Preco) + SUM(ip.Quantidade * p.Preco) AS Total
   FROM OrdemServico os
   LEFT JOIN ItemServico iserv ON os.OrdemServicoID = iserv.OrdemServicoID
   LEFT JOIN Servico s ON iserv.ServicoID = s.ServicoID
   LEFT JOIN ItemPeca ip ON os.OrdemServicoID = ip.OrdemServicoID
   LEFT JOIN Peca p ON ip.PecaID = p.PecaID
   GROUP BY os.OrdemServicoID;

**4.** Defina ordenações dos dados com ORDER BY:
Recuperar todos os clientes em ordem alfabética: SELECT * FROM Cliente ORDER BY Nome;
Recuperar todas as ordens de serviço em ordem decrescente de data de abertura: SELECT * FROM OrdemServico ORDER BY DataAbertura DESC;

**5.** Condições de filtros aos grupos – HAVING Statement:
Recuperar os clientes que fizeram mais de 3 ordens de serviço:
   SELECT c.ClienteID, c.Nome, COUNT(os.OrdemServicoID) AS TotalOrdens
   FROM Cliente c
   LEFT JOIN Veiculo v ON c.ClienteID = v.ClienteID
   LEFT JOIN OrdemServico os ON v.VeiculoID = os.VeiculoID
   GROUP BY c.ClienteID, c.Nome
   HAVING COUNT(os.OrdemServicoID) > 3;

**6.** Crie junções entre tabelas para fornecer uma perspectiva mais complexa dos dados:
Recuperar todas as ordens de serviço com detalhes do cliente, veículo e serviços realizados:
   SELECT os.OrdemServicoID, c.Nome AS Cliente, v.Placa AS Veiculo, s.Nome AS Servico
   FROM OrdemServico os
   LEFT JOIN Veiculo v ON os.VeiculoID = v.VeiculoID
   LEFT JOIN Cliente c ON v.ClienteID = c.ClienteID
   LEFT JOIN ItemServico iserv ON os.OrdemServicoID = iserv.OrdemServicoID
   LEFT JOIN Servico s ON iserv.ServicoID = s.ServicoID;
