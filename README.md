# ProjetoForms1.FrmProdutos
Formulario de cadastro de produtos

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace ProjetoForms1
{
    public partial class FrmProdutos : Form
    {
        public FrmProdutos()
        {
            InitializeComponent();
            AtualizarGrid();
            //carregar o combo fornecedor
            cbFornecedor.DataSource = listarFornecedores();
            cbFornecedor.DisplayMember = "nome";
            cbFornecedor.ValueMember = "codigo";

  cbMarca.DataSource = listarMarcas();
            cbMarca.DisplayMember = "nome";
            cbMarca.ValueMember = "codigo";
        }
        //Criar a instância do SQLConnection
        SqlConnection con = new SqlConnection(Dados.Conexao);


  public static DataTable listarFornecedores()
        {
            try
            {
                SqlConnection con=new SqlConnection(Dados.Conexao);
                con.Open();
                string sqlLista = "Select codigo,nome from fornecedor";
                SqlDataAdapter da = new SqlDataAdapter(sqlLista, con);
                DataTable dt = new DataTable();
                da.Fill(dt);
                con.Close();
                return dt;
            }
            catch (SqlException erro)
            {
                MessageBox.Show(erro.Message);
                return null;
            }
        }
        public static DataTable listarMarcas()
        {
            try
            {
                SqlConnection con = new SqlConnection(Dados.Conexao);
                con.Open();
                string sqlLista = "Select codigo,nome from marca";
                SqlDataAdapter da = new SqlDataAdapter(sqlLista, con);
                DataTable dt = new DataTable();
                da.Fill(dt);
                con.Close();
                return dt;
            }
            catch (SqlException erro)
            {
                MessageBox.Show("[prod0001] - Erro ao listar marca: "+erro.Message);
                return null;
            }
        }
        private void txtNome_TextChanged(object sender, EventArgs e)
        {

  }

  private void label2_Click(object sender, EventArgs e)
        {

  }
        private void LimparTudo()
        {
            txtCodigo.Clear();
            txtNome.Clear();
            txtDescricao.Clear();
            txtQuantidade.Clear();
            txtValUnitario.Clear();
            cbFornecedor.SelectedIndex = -1;
            cbMarca.SelectedIndex = -1;
            txtNome.Focus();
        }
        private void btnLimpar_Click(object sender, EventArgs e)
        {
            LimparTudo();
        }

  private void btnAlterar_Click(object sender, EventArgs e)
        {
            try
            {
                DialogResult resp = MessageBox.Show("Deseja realmente alterar?",
                    "Sistema TI35", MessageBoxButtons.YesNo,
                    MessageBoxIcon.Question);
                if (resp == DialogResult.Yes)
                {
                    //vai efetuar as alterações daquele cliente em questão
                    ProdutoInformation p = new ProdutoInformation();
                    p.Codigo = Convert.ToInt32(txtCodigo.Text);
                    p.Fornecedor_codigo = Convert.ToInt32(cbFornecedor.SelectedValue);
                    p.Marca_codigo = Convert.ToInt32(cbMarca.SelectedValue);
                    p.Nome = txtNome.Text;
                    p.Quantidade = Convert.ToInt32(txtQuantidade.Text);
                    p.Valorunitario=Convert.ToDecimal(txtValUnitario.Text);
                    p.Descricao = txtDescricao.Text;

  //abrir o banco
                    con.Open();
                    //instrução sql para alterar
                    string sqlAlterar = "Update produto set fornecedor_codigo=@fornecedor_codigo, " +
                        "marca_codigo=@marca_codigo,nome=@nome,quantidade=@quantidade," +
                        "valorunitario=@valorunitario," +
                        "descricao=@descricao where codigo=@codigo";
                    SqlCommand cmdAlterar = new SqlCommand(sqlAlterar, con);
                    cmdAlterar.Parameters.Add("fornecedor_codigo",SqlDbType.Int).Value=
                        cbFornecedor.SelectedValue;
                    cmdAlterar.Parameters.Add("marca_codigo", SqlDbType.Int).Value =
                        cbMarca.SelectedValue;
                    cmdAlterar.Parameters.Add("@nome", SqlDbType.VarChar).Value =
                        p.Nome;
                    cmdAlterar.Parameters.Add("@quantidade", SqlDbType.Int).Value =
                       Convert.ToInt32(p.Quantidade);
                    cmdAlterar.Parameters.Add("@valorunitario", SqlDbType.Decimal).Value =
                       Convert.ToDecimal(p.Valorunitario);
                    cmdAlterar.Parameters.Add("@descricao", SqlDbType.VarChar).Value =
                     p.Descricao;
                    cmdAlterar.Parameters.Add("@codigo", SqlDbType.Int).Value =
                        Convert.ToInt32(p.Codigo);

  //se tudo estiver ok, vai executar a query
                    cmdAlterar.ExecuteNonQuery();
                    con.Close();
                    AtualizarGrid();
                    LimparTudo();
                }
            }
            catch (SqlException erro)
            {

  }
        }
        public void AtualizarGrid()
        {
            try
            {
                dgProduto.DataSource = listaProdutos();

  //configuração do cabeçalho do dgCliente
                dgProduto.Columns[0].HeaderText = "Cód.";
                dgProduto.Columns[1].HeaderText = "Fornec.";
                dgProduto.Columns[2].HeaderText = "Marca";
                dgProduto.Columns[3].HeaderText = "Nome";
                dgProduto.Columns[4].HeaderText = "Quant.";
                dgProduto.Columns[5].HeaderText = "Vl.Unitário";
                dgProduto.Columns[6].HeaderText = "Descrição";

  //configuração da largura do dgCliente
                dgProduto.Columns[0].Width = 80;
                dgProduto.Columns[1].Width = 120;
                dgProduto.Columns[2].Width = 120;
                dgProduto.Columns[3].Width = 200;
                dgProduto.Columns[4].Width = 150;
                dgProduto.Columns[5].Width = 150;
                dgProduto.Columns[6].Width = 150;

  //configuração de permissão
                dgProduto.SelectionMode =
                    DataGridViewSelectionMode.FullRowSelect;
                dgProduto.AllowUserToDeleteRows = false;
                dgProduto.AllowUserToAddRows = false;
                dgProduto.ReadOnly = true;
            }
            catch (SqlException erro)
            {
                MessageBox.Show("Erro: " + erro.Message);
            }
        }
        public static DataTable listaProdutos()
        {
            try
            {
                SqlConnection con = new SqlConnection(Dados.Conexao);
                con.Open();
                string sqlListar = "Select * from produto";
                //vamos utilizar uma classe adaptador para receber os
                //dados da tabela cliente
                SqlDataAdapter da = new SqlDataAdapter(sqlListar, con);
                //estamos chamando uma classe do tipo Tabela
                DataTable dt = new DataTable();
                da.Fill(dt);
                con.Close();
                return dt;
           

  }
            catch (SqlException erro)
            {
                return null;
            }
        }

  private void FrmProdutos_Load(object sender, EventArgs e)
        {

  }

  private void btnInserir_Click(object sender, EventArgs e)
        {
            try
            {
                //inserir produtos
                con.Open();
                string sqlInserir = "Insert into produto(fornecedor_codigo," +
                    "marca_codigo,nome,quantidade,valorunitario,descricao)" +
                    "values(@fornecedor_codigo,@marca_codigo,@nome,@quantidade," +
                    "@valorunitario,@descricao)";
                SqlCommand cmd = new SqlCommand(sqlInserir, con);
                cmd.Parameters.Add("@fornecedor_codigo", SqlDbType.Int).Value =
                   Convert.ToInt32(cbFornecedor.SelectedValue);
                cmd.Parameters.Add("@marca_codigo", SqlDbType.Int).Value =
                    Convert.ToInt32(cbMarca.SelectedValue);
                cmd.Parameters.Add("@nome", SqlDbType.VarChar).Value = 
                    txtNome.Text;
                cmd.Parameters.Add("@quantidade",SqlDbType.Int).Value =
                    Convert.ToInt32(txtQuantidade.Text);
                cmd.Parameters.Add("@valorunitario",SqlDbType.Decimal).Value=
                    Convert.ToDecimal(txtValUnitario.Text);
                cmd.Parameters.Add("@descricao",SqlDbType.VarChar).Value=
                    txtDescricao.Text;
                cmd.ExecuteNonQuery();
                con.Close();
                MessageBox.Show("Produto inserido com sucesso!");
                AtualizarGrid();
                LimparTudo();


  }
            catch (SqlException erro)
            {
                MessageBox.Show(erro.Message);
            }
        }

  private void dgProduto_MouseClick(object sender, MouseEventArgs e)
        {
            try
            {
                int linha = dgProduto.SelectedRows[0].Index;
                
  if (linha >= 0)
                {
                    
  int codigo = Convert.ToInt32
                        (dgProduto.Rows[linha].Cells[0].Value);
                    ProdutoInformation p = selecionarProduto(codigo);
                    txtCodigo.Text = p.Codigo.ToString();
                    cbFornecedor.SelectedValue=p.Fornecedor_codigo.ToString();
                    cbMarca.SelectedValue=p.Marca_codigo.ToString() ;
                    txtNome.Text = p.Nome.ToString();
                    txtQuantidade.Text = p.Quantidade.ToString();
                    txtValUnitario.Text=p.Valorunitario.ToString();
                    txtDescricao.Text= p.Descricao.ToString();
                }
            }
            catch (SqlException erro)
            {

  }
        }
        public static ProdutoInformation selecionarProduto(int codigo)
        {
            try
            {
                SqlConnection con = new SqlConnection(Dados.Conexao);
                con.Open();
                string sqlSelecionar = "Select * from produto where codigo=@codigo";
                SqlCommand cmd = new SqlCommand(sqlSelecionar, con);
                cmd.Parameters.Add("@codigo", SqlDbType.Int).Value = codigo;
                //como precisa trazer apenas as inf´s do código específico, ele vai
                //varrer os registros e trazer somente o que for válido
                SqlDataReader dr = cmd.ExecuteReader();
                //se houver dados para efetuar a busca, vai varrer até o último reg.
                if (dr.Read())
                {
                    ProdutoInformation p = new ProdutoInformation();
                    p.Codigo = Convert.ToInt32(dr[0]);
                    p.Fornecedor_codigo = Convert.ToInt32(dr[1]);
                    p.Marca_codigo = Convert.ToInt32(dr[2]);
                    p.Nome = dr[3].ToString();
                    p.Quantidade = Convert.ToInt32(dr[4]);
                    p.Valorunitario = Convert.ToDecimal(dr[5]);
                    p.Descricao = dr[6].ToString();

  con.Close();
                    return p;
                }
                else
                {
                    con.Close();
                    return null;
                }

  }
            catch (SqlException erro)
            {
                return null;
            }
        }

  private void txtPesquisaNome_TextChanged(object sender, EventArgs e)
        {
            try
            {
                dgProduto.DataSource = pesquisaProdutos(txtPesquisaNome.Text);

  //configuração do cabeçalho do dgCliente
                dgProduto.Columns[0].HeaderText = "Cód.";
                dgProduto.Columns[1].HeaderText = "Fornec.";
                dgProduto.Columns[2].HeaderText = "Marca";
                dgProduto.Columns[3].HeaderText = "Nome";
                dgProduto.Columns[4].HeaderText = "Quant.";
                dgProduto.Columns[5].HeaderText = "Vl.Unitário";
                dgProduto.Columns[6].HeaderText = "Descrição";

  //configuração da largura do dgCliente
                dgProduto.Columns[0].Width = 80;
                dgProduto.Columns[1].Width = 120;
                dgProduto.Columns[2].Width = 120;
                dgProduto.Columns[3].Width = 200;
                dgProduto.Columns[4].Width = 150;
                dgProduto.Columns[5].Width = 150;
                dgProduto.Columns[6].Width = 150;

  //configuração de permissão
                dgProduto.SelectionMode =
                    DataGridViewSelectionMode.FullRowSelect;
                dgProduto.AllowUserToDeleteRows = false;
                dgProduto.AllowUserToAddRows = false;
                dgProduto.ReadOnly = true;
            }
            catch (SqlException erro)
            {
                MessageBox.Show("Erro: " + erro.Message);
            }
        }
        public static DataTable pesquisaProdutos(string nome)
        {
            try
            {
                SqlConnection con = new SqlConnection(Dados.Conexao);
                con.Open();
                string sqlPesquisar =
                    "Select * from produto where nome like @nome";
                SqlDataAdapter da = new SqlDataAdapter(sqlPesquisar, con);
                da.SelectCommand.Parameters.Add("@nome", SqlDbType.VarChar).Value =
                    "%" + nome + "%";
                DataTable dt = new DataTable();
                da.Fill(dt);
                con.Close();
                return dt;
                
  }
            catch (SqlException erro)
            {
                MessageBox.Show("Erro ao Pesquisar o nome do Fornecedor!"
                    + erro.Message);
                return null;
            }
        }
    }
}
