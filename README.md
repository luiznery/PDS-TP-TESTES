# Trabalho Pratico - Testes

O repositorio escolhido para analisar os testes foi o da biblioteca python, <a href="https://github.com/pandas-dev/pandas">Pandas</a>

## 1. Series.fillna
- <a href="">Referencia</a>
- <a href="">Codigo</a>Codigo
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
Esse teste pretende fazer testes de comportamento no metodo fillna da classe Series. Primeiro, testa se os metodos "ffill" e "backfill" estão funcionando, respectivamente esse metodos substituem um valor invalido pelo primeiro valor valido anterior e pelo primeiro valor valido posterior. Em seguida, é testada a substituição por um valor especifico passado como parametro. E por fim, testa-se se o metodo levanta algum erro caso nenhum metodo ou valor seja passado.


## 2.
- <a href="">Referencia</a>
- <a href="">Codigo</a>
```python
```

## 3. 
- <a href="">Referencia</a>
- <a href="">Codigo</a>
```python
```
