# Trabalho Pratico - Testes

O repositorio escolhido para analisar os testes foi o da biblioteca python, <a href="https://github.com/pandas-dev/pandas">Pandas</a>.

## 1. Series.fillna
- <a href="https://pandas.pydata.org/docs/reference/api/pandas.Series.fillna.html">Referencia</a>
- <a href="https://github.com/pandas-dev/pandas/blob/7fbb8d146097ff1fa6a893b110b80876d971ebac/pandas/tests/series/methods/test_fillna.py#L69">Codigo</a>
```python
def test_fillna(self):
    ts = Series([0.0, 1.0, 2.0, 3.0, 4.0], index=tm.makeDateIndex(5))

    tm.assert_series_equal(ts, ts.fillna(method="ffill"))

    ts[2] = np.NaN

    exp = Series([0.0, 1.0, 1.0, 3.0, 4.0], index=ts.index)
    tm.assert_series_equal(ts.fillna(method="ffill"), exp)

    exp = Series([0.0, 1.0, 3.0, 3.0, 4.0], index=ts.index)
    tm.assert_series_equal(ts.fillna(method="backfill"), exp)

    exp = Series([0.0, 1.0, 5.0, 3.0, 4.0], index=ts.index)
    tm.assert_series_equal(ts.fillna(value=5), exp)

    msg = "Must specify a fill 'value' or 'method'"
    with pytest.raises(ValueError, match=msg):
        ts.fillna()
```
Este teste pretende fazer testes de comportamento no método fillna da classe Series. Primeiro, testa se os métodos "ffill" e "backfill" estão funcionando, respectivamente esse métodos substituem um valor invalido pelo primeiro valor válido anterior e pelo primeiro valor válido posterior. Em seguida, é testada a substituição por um valor especifico passado como parâmetro. E por fim, testa-se se o método levanta algum erro caso nenhum método ou valor seja passado.


## 2. Series.fillna
- <a href="https://pandas.pydata.org/docs/reference/api/pandas.Series.fillna.html">Referencia</a>
- <a href="https://github.com/pandas-dev/pandas/blob/7fbb8d146097ff1fa6a893b110b80876d971ebac/pandas/tests/series/methods/test_fillna.py#L192">Codigo</a>
```python
def test_timedelta_fillna(self, frame_or_series):
    # GH#3371
    ser = Series(
        [
            Timestamp("20130101"),
            Timestamp("20130101"),
            Timestamp("20130102"),
            Timestamp("20130103 9:01:01"),
        ]
    )
    td = ser.diff()
    obj = frame_or_series(td)

    # reg fillna
    result = obj.fillna(Timedelta(seconds=0))
    expected = Series(
        [
            timedelta(0),
            timedelta(0),
            timedelta(1),
            timedelta(days=1, seconds=9 * 3600 + 60 + 1),
        ]
    )
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

    # interpreted as seconds, no longer supported
    msg = "value should be a 'Timedelta', 'NaT', or array of those. Got 'int'"
    with pytest.raises(TypeError, match=msg):
        obj.fillna(1)

    result = obj.fillna(Timedelta(seconds=1))
    expected = Series(
        [
            timedelta(seconds=1),
            timedelta(0),
            timedelta(1),
            timedelta(days=1, seconds=9 * 3600 + 60 + 1),
        ]
    )
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

    result = obj.fillna(timedelta(days=1, seconds=1))
    expected = Series(
        [
            timedelta(days=1, seconds=1),
            timedelta(0),
            timedelta(1),
            timedelta(days=1, seconds=9 * 3600 + 60 + 1),
        ]
    )
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

    result = obj.fillna(np.timedelta64(10 ** 9))
    expected = Series(
        [
            timedelta(seconds=1),
            timedelta(0),
            timedelta(1),
            timedelta(days=1, seconds=9 * 3600 + 60 + 1),
        ]
    )
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

    result = obj.fillna(NaT)
    expected = Series(
        [
            NaT,
            timedelta(0),
            timedelta(1),
            timedelta(days=1, seconds=9 * 3600 + 60 + 1),
        ],
        dtype="m8[ns]",
    )
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

    # ffill
    td[2] = np.nan
    obj = frame_or_series(td)
    result = obj.ffill()
    expected = td.fillna(Timedelta(seconds=0))
    expected[0] = np.nan
    expected = frame_or_series(expected)

    tm.assert_equal(result, expected)

    # bfill
    td[2] = np.nan
    obj = frame_or_series(td)
    result = obj.bfill()
    expected = td.fillna(Timedelta(seconds=0))
    expected[2] = timedelta(days=1, seconds=9 * 3600 + 60 + 1)
    expected = frame_or_series(expected)
    tm.assert_equal(result, expected)

```
No começo da função de teste se inicia um objeto Series contendo 4 valores de Timestamp, em seguida se é calculada a diferença entre eles o que gera uma Série de Timedelta, sendo o primeiro elemento nulo. 

O primeiro teste é simples, objetivando verificar se o elemento nulo é trocado por um do tipo Timedelta com valor 0. Depois, testa se ao passar um valor de um tipo diferente do da Série, o método lança um erro. Em seguida se repete o mesmo teste para alguns valores aumentando a complexidade: Timedelta de 1 segundo, Timedelta de 1 dia e 1 segundo, outros tipos de timedelta da biblioteca numpy e valor nulo. 

Em seguida os métodos ffill e bfill são testados, é esperado que o primeiro, no caso dado, não faça nada pois o valor invalido é o primeiro e o segundo preencha a posição inválida com o valor da próxima posição válida.


## 3. Series.value_counts
- <a href="https://pandas.pydata.org/docs/reference/api/pandas.Series.value_counts.html">Referencia</a>
- <a href="https://github.com/pandas-dev/pandas/blob/eabac61caeebac4c3f125ca7aaddd3d08686f296/pandas/tests/series/methods/test_value_counts.py#L14">Codigo</a>
```python
def test_value_counts_datetime(self):
    # most dtypes are tested in tests/base
    values = [
        pd.Timestamp("2011-01-01 09:00"),
        pd.Timestamp("2011-01-01 10:00"),
        pd.Timestamp("2011-01-01 11:00"),
        pd.Timestamp("2011-01-01 09:00"),
        pd.Timestamp("2011-01-01 09:00"),
        pd.Timestamp("2011-01-01 11:00"),
    ]

    exp_idx = pd.DatetimeIndex(
        ["2011-01-01 09:00", "2011-01-01 11:00", "2011-01-01 10:00"]
    )
    exp = Series([3, 2, 1], index=exp_idx, name="xxx")

    ser = Series(values, name="xxx")
    tm.assert_series_equal(ser.value_counts(), exp)
    # check DatetimeIndex outputs the same result
    idx = pd.DatetimeIndex(values, name="xxx")
    tm.assert_series_equal(idx.value_counts(), exp)

    # normalize
    exp = Series(np.array([3.0, 2.0, 1]) / 6.0, index=exp_idx, name="xxx")
    tm.assert_series_equal(ser.value_counts(normalize=True), exp)
    tm.assert_series_equal(idx.value_counts(normalize=True), exp)
```
Esse teste já busca testar um outro método da classe Series, o método 'value_counts'. Esse teste específico deseja testar esse método para séries com valores do tipo Timestamp. Primeiro se testa se o método retorna o valor esperado aplicando ele a aos valores de uma série. Em seguida se aplica o mesmo método a uma série de índices. E por fim, se testa se o método retorna o valor esperado quando o parâmetro normalize é passado como True.
