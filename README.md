import heapq

# Definir los pasos de inspección y sus costos (tiempo en segundos, por ejemplo)
INSPECCIONES = {
    'inicio': [('visual', 2)],
    'visual': [('peso', 3), ('dimensiones', 4), ('meta', 5)],
    'peso': [('funcionamiento', 5), ('meta', 1)],
    'dimensiones': [('meta', 2)],
    'funcionamiento': [('meta', 3)]
}

# Heurística simplificada: estimación de la cantidad mínima de pasos restantes para alcanzar la meta
def heuristica(estado_actual, meta):
    if estado_actual == 'meta':
        return 0
    return 1  # En este ejemplo, asumimos que siempre está a un paso de la meta

# Algoritmo A* para encontrar el camino más corto hacia la detección del defecto
def a_star(INSPECCIONES, inicio, meta):
    abiertos = []
    heapq.heappush(abiertos, (0 + heuristica(inicio, meta), 0, inicio))  # (f(n), g(n), estado)
    recorrido = {}
    costo_acumulado = {inicio: 0}
    
    while abiertos:
        _, costo_actual, estado_actual = heapq.heappop(abiertos)

        # Si alcanzamos el estado "meta", reconstruimos el camino
        if estado_actual == meta:
            ruta = []
            costos = []
            while estado_actual in recorrido:
                ruta.append(estado_actual)
                costos.append(costo_acumulado[estado_actual])
                estado_actual = recorrido[estado_actual]
            ruta.append(estado_actual)  # Agregar el nodo inicial
            costos.append(costo_acumulado[estado_actual])  # Agregar el costo inicial
            ruta.reverse()
            costos.reverse()
            return ruta, costos

        # Expandir las inspecciones posibles desde el estado actual
        for inspeccion, costo in INSPECCIONES.get(estado_actual, []):
            nuevo_costo = costo_actual + costo

            # Si el estado vecino no ha sido visitado o encontramos un costo menor
            if inspeccion not in costo_acumulado or nuevo_costo < costo_acumulado[inspeccion]:
                recorrido[inspeccion] = estado_actual
                costo_acumulado[inspeccion] = nuevo_costo
                f = nuevo_costo + heuristica(inspeccion, meta)
                heapq.heappush(abiertos, (f, nuevo_costo, inspeccion))

    return None, None

# Ejecutar el algoritmo A*
ruta_optima, costos_optimos = a_star(INSPECCIONES, 'inicio', 'meta')

# Mostrar el resultado
if ruta_optima:
    print("Ruta óptima de inspección con costos:")
    for paso, costo in zip(ruta_optima, costos_optimos):
        print(f"{paso} (Costo acumulado: {costo})")
else:
    print("No se encontró una ruta.")
