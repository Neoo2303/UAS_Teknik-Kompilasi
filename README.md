# UAS_Teknik-Kompilasi
Nama : Borneo Paradis Anwar
Nim  : 231011400143
Kelas: 06TPLE003
Tugas Proyek Akhir
Representasi Tahapan Kompilasi
Konstruksi: Perulangan While
1. Pattern (BNF)
<while_stmt> ::= "while" "(" <condition> ")" "{" <statement> "}"

<condition> ::= <identifier> <operator> <value>

<statement> ::= <identifier> "=" <expression>

<expression> ::= <identifier> "+" <value>
               | <identifier> "-" <value>
               | <value>

<identifier> ::= letter {letter | digit}

<operator> ::= "<" | ">" | "<=" | ">=" | "==" | "!="

<value> ::= number
2. Source Code (Python)
class WhileCompiler:

    def __init__(self, source):
        self.source = source
        self.label = 1

    def new_label(self):
        lbl = f"L{self.label}"
        self.label += 1
        return lbl

    # Tahap 1 : Analisis Leksikal
    def lexical_analysis(self):
        symbols = ['(', ')', '{', '}', '=', '+', '-', '<', '>']
        text = self.source

        for s in symbols:
            text = text.replace(s, f' {s} ')

        tokens = text.split()
        return tokens

    # Tahap 2 : Analisis Sintaksis (AST sederhana)
    def syntax_analysis(self, tokens):

        if tokens[0] != "while":
            raise SyntaxError("Harus diawali while")

        condition = " ".join(tokens[2:5])

        body = " ".join(tokens[7:-1])

        ast = {
            "type": "WhileStatement",
            "condition": condition,
            "body": body
        }

        return ast

    # Tahap 3 : Analisis Semantik
    def semantic_analysis(self, ast):

        identifiers = []

        cond = ast["condition"].split()

        identifiers.append(cond[0])

        body = ast["body"].split()

        identifiers.append(body[0])

        print("Variabel ditemukan :", identifiers)

        print("Pengecekan semantik berhasil.")

    # Tahap 4 : Three Address Code
    def generate_TAC(self, ast):

        start = self.new_label()
        end = self.new_label()

        tac = []

        tac.append(f"{start}:")
        tac.append(f"ifFalse {ast['condition']} goto {end}")
        tac.append(ast["body"])
        tac.append(f"goto {start}")
        tac.append(f"{end}:")

        return "\n".join(tac)


source = "while ( x < 10 ) { x = x + 1 }"

compiler = WhileCompiler(source)

tokens = compiler.lexical_analysis()

print("===== ANALISIS LEKSIKAL =====")
print(tokens)

ast = compiler.syntax_analysis(tokens)

print("\n===== ABSTRACT SYNTAX TREE =====")
print(ast)

print("\n===== ANALISIS SEMANTIK =====")
compiler.semantic_analysis(ast)

print("\n===== THREE ADDRESS CODE =====")
print(compiler.generate_TAC(ast))
3. Output Program
Analisis Leksikal
['while',
'(',
'x',
'<',
'10',
')',
'{',
'x',
'=',
'x',
'+',
'1',
'}']
Abstract Syntax Tree (AST)
{
    'type': 'WhileStatement',
    'condition': 'x < 10',
    'body': 'x = x + 1'
}
Analisis Semantik
Variabel ditemukan : ['x', 'x']
Pengecekan semantik berhasil.
Three Address Code (TAC)
L1:
ifFalse x < 10 goto L2
x = x + 1
goto L1
L2:
4. Penjelasan Tahapan
a. Analisis Leksikal

Tahap ini memecah source code menjadi token-token seperti keyword (while), identifier (x), operator (<, =), angka (10, 1), dan simbol ((, ), {, }).

b. Analisis Sintaksis

Token yang diperoleh kemudian diperiksa apakah sesuai dengan aturan grammar (BNF). Setelah valid, token disusun menjadi Abstract Syntax Tree (AST) yang merepresentasikan struktur program.

c. Analisis Semantik

Pada tahap ini dilakukan pemeriksaan sederhana terhadap variabel yang digunakan pada kondisi dan isi perulangan. Jika variabel dikenali dan tidak ada kesalahan dasar, maka analisis semantik dinyatakan berhasil.

d. Generasi Three Address Code (TAC)

AST diterjemahkan menjadi kode antara (Intermediate Code) menggunakan label. TAC mempermudah proses optimasi sebelum menghasilkan kode mesin.

Contoh TAC:

L1:
ifFalse x < 10 goto L2
x = x + 1
goto L1
L2:

Kode tersebut menunjukkan bahwa selama kondisi x < 10 bernilai benar, perintah x = x + 1 akan terus dijalankan. Jika kondisi bernilai salah, program akan melompat ke label L2 dan keluar dari perulangan.
