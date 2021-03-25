# Ejercicios para AWK

Ejercicios para practicar awk

## 1

data/motus_profile

Este es un fichero procedente del análisis, de una muestra de secuenciación de DNA metagenómica, con mOTUs v2.5.1 https://github.com/motu-tool/mOTUs_v2.
Dados unos "reads", el programa los mapea contra una serie de genes marcadores, e intenta dar un perfil de especies presentes en la muestra.

El fichero tiene una cabecera (filas que comienzan por "#"), y luego filas de 4 campos.


1.1 Comencemos por ver la cabecera del fichero. Podemos hacer esto fácilmente con grep:

```
grep "^#" data/motus_profile
```

salida del grep:
```
# git tag version 2.5.1 |  motus version 2.5.1
# call: python motus profile
#mOTU   consensus_taxonomy      NCBI_tax_id     16s001178
```

¿Cómo hacemos esto mismo con awk?

solución 1.1: `awk '/^#/' data/motus_profile`

salida esperada:
la misma que para el grep

Deberiamos ver que el fichero tiene las columnas:
- mOTU: un identificador para el grupo identificado.
- consensus_taxonomy: la clasificación taxonómica consenso para dicho mOTU.
- NCBI_tax_id: el identificador de grupo taxonómico en la base de datos NCBI Taxonomy.
- 16s001178: este es el nombre de la muestra analizada en este experimento en concreto. La columna contendrá la abundancia relativa de este mOTU en la muestra.

-------------

1.2. Ahora veamos los valores para la primera fila de datos. Podemos hacer esto fácilmente con:

```
grep -v "^#" data/motus_profile | head -1
```

salida del grep:
```
ref_mOTU_v25_00001      Leptospira alexanderi   100053  0.0000000000
```

¿Cómo hacemos esto mismo con awk?

solución 1.2: `awk '/^#/ {next} {print $0; exit}' data/motus_profile`

salida esperada:
la misma que para el grep

----

1.3. Veamos a continuación algunos valores de la columna "consensus_taxonomy", que es la segunda columna. Por ejemplo, vamos a mostrar los primeros 10 valores de dicha columna.

```
grep -v "^#" data/motus_profile | head -10 | cut -f 2
```

salida del comando:
```
Leptospira alexanderi
Leptospira weilii [Leptospira sp. P2653/Leptospira weilii]
Chryseobacterium sp. [Chryseobacterium sp. OV705/Chryseobacterium sp. YR221]
Chryseobacterium gallinarum [Chryseobacterium gallinarum/Chryseobacterium sp. P1-3]
Chryseobacterium indologenes [Chryseobacterium sp. UNC8MFCol/Chryseobacterium indologenes]
Chryseobacterium artocarpi/ureilyticum
Chryseobacterium jejuense [Chryseobacterium jejuense/Chryseobacterium sp. StRB126]
Chryseobacterium sp. G972
Chryseobacterium contaminans
Chryseobacterium indologenes
```