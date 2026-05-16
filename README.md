import pandas as pd

def conciliacion_automatica():
    print("Iniciando Conciliación Bancaria con IA y Python...\n" + "-"*50)

    # 1. TUS DATOS CONTABLES (Auxiliar)
    # En la vida real, la IA leería esto con: df_conta = pd.read_csv('mi_contabilidad.csv')
    df_conta = pd.DataFrame({
        'Fecha': ['2023-10-01', '2023-10-02', '2023-10-05', '2023-10-08'],
        'Referencia': ['Factura 001', 'Pago a Proveedor A', 'Factura 002', 'Cheque 099'],
        'Monto': [5400.00, -1200.00, 3100.00, -800.00] # Positivos son entradas, negativos salidas
    })

    # 2. EL ESTADO DE CUENTA DEL BANCO
    # En la vida real: df_banco = pd.read_csv('estado_de_cuenta_banorte.csv')
    df_banco = pd.DataFrame({
        'Fecha': ['2023-10-01', '2023-10-03', '2023-10-06', '2023-10-31'],
        'Concepto': ['Transferencia SPEI', 'Cobro Proveedor A', 'Transferencia SPEI', 'Comisión por Manejo de Cuenta'],
        'Monto': [5400.00, -1200.00, 3100.00, -350.00]
    })

    # 3. EL CEREBRO DE LA OPERACIÓN (Cruce de datos)
    # Unimos ambas tablas basándonos en el MONTO exacto.
    # El indicador (indicator=True) nos dirá de dónde viene cada registro.
    cruce = pd.merge(
        df_conta, 
        df_banco, 
        on='Monto', 
        how='outer', 
        suffixes=('_Conta', '_Banco'),
        indicator=True
    )

    # 4. SEPARAR RESULTADOS SEGÚN SU ESTADO
    # 'both': Está en contabilidad y en el banco = CONCILIADO
    conciliados = cruce[cruce['_merge'] == 'both']
    
    # 'left_only': Solo está en tu contabilidad = EN TRÁNSITO
    en_transito = cruce[cruce['_merge'] == 'left_only']
    
    # 'right_only': Solo está en el banco = FALTA REGISTRAR
    no_registrados = cruce[cruce['_merge'] == 'right_only']

    # 5. MOSTRAR EL REPORTE FINAL
    print("✅ MOVIMIENTOS CONCILIADOS (Todo cuadra perfectamente):")
    print(conciliados[['Monto', 'Referencia', 'Concepto']].to_string(index=False))
    
    print("\n⏳ PARTIDAS EN TRÁNSITO (En tus libros, pero el banco aún no lo procesa):")
    # Ejemplo: Entregaste un cheque pero el proveedor no lo ha cobrado.
    print(en_transito[['Fecha_Conta', 'Referencia', 'Monto']].dropna().to_string(index=False))
    
    print("\n⚠️ PARTIDAS NO REGISTRADAS (El banco te cobró/pagó, pero no está en tu sistema):")
    # Ejemplo: Comisiones bancarias, impuestos o depósitos de clientes que no avisaron.
    print(no_registrados[['Fecha_Banco', 'Concepto', 'Monto']].dropna().to_string(index=False))

if __name__ == "__main__":
    conciliacion_automatica()
