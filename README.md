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

salida del comando que usa grep:
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

¿Cómo hacemos esto mismo con awk?

solución 1.3: `awk -F '\t' '/^#/ {next;} i < 10 {print $2; i++;} i >= 10 {exit}' data/motus_profile`

salida esperada:
la misma que con el comando que usa grep

----

1.4. Ahora vamos ya al meollo de la cuestión. En el fichero tenemos los datos para todos los mOTUs. Queremos ver cuántos tenemos en nuestra muestra, es decir, cuántos hay con abundancia relativa > 0. Esto ya no es tan fácil de hacer sin utilizar awk. En concreto, vamos a mostrar el total de mOTUs, cuántos no han sido detectados y cuántos sí han sido detectados en la muestra.

¿Cómo lo hacemos con awk?

solución 1.4: `awk -F '\t' '/^#/ {next} $4 == 0.0 {i++} $4 > 0.0 {j++} END {print i+j,i,j}' data/motus_profile`

salida esperada:
```
14213 13521 692
```


----

1.5. ¿Cómo podemos hacer para en lugar de la salida de 1.4 obtener la salida en el siguiente formato?

```
14213,13521,692
```

solución 1.5: `awk -F '\t' 'BEGIN{OFS=","} /^#/ {next} $4 == 0.0 {i++} $4 > 0.0 {j++} END {print i+j,i,j}' data/motus_profile`

----

1.6. ¿Y en el siguiente formato?

```
14213 = 13521 + 692
```

solución 1.6: `awk -F '\t' '/^#/ {next} $4 == 0.0 {i++} $4 > 0.0 {j++} END {printf "%s = %s + %s\n",i+j,i,j}' data/motus_profile`

----

1.7. Así que tenemos 692 mOTUs detectados en la muestra. Imprime solamente esos 692 a un nuevo fichero, pero solamente con los siguientes campos, separados por "|":

- Género
- Especie
- Abundancia relativa

solución 1.7: `awk -F '\t' 'BEGIN{OFS="|"} /^#/ {next} $4 > 0.0 {sub(" .*", "", $2); print $2,$4 > "motus_detected_genera"}' data/motus_profile`

salida esperada (contenido de "motus_detected_genera"):
```
Vibrio|0.0000298200
Vibrio|0.0000058563
Proteobacteria|0.0000010894
Enterobacteriaceae|0.0000007736
Pseudomonas|0.0000005643
Streptococcus|0.0000240375
Streptococcus|0.0000393340
Streptococcus|0.0000117978
Streptococcus|0.0000065401
Streptococcus|0.0000041700
...
```

----

1.8. Procesa el fichero obtenido en 1.7, para mostrar una sola línea por género, incluyendo como columnas el nombre del género y la suma de los valores de abundancia para ese género, dichos campos separados por tabulador.

solución 1.8: `awk -F '|' 'BEGIN{OFS="\t"}{v[$1]+=$2} END {for (a in v) print a,v[a]}' motus_detected_genera`

salida esperada:
```
uncultured      0.000332116
Lactococcus     8.90441e-05
Pseudonocardia  2.3255e-06
Insolitispirillum       2.0513e-06
Aggregatibacter 7.711e-07
Chitinimonas    7.793e-07
Bradyrhizobium  7.679e-07
Romboutsia      1.60966e-05
Niameybacter    1.2874e-06
Bacteroidaceae  7.34845e-05
...
```

----

1.9. Haz lo mismo que en 1.8, pero mostrando los géneros ordenados alfabéticamente.
tip: usa la función "asorti" https://www.gnu.org/software/gawk/manual/html_node/Array-Sorting-Functions.html


solución 1.9: `awk -F '|' 'BEGIN{OFS="\t"}{v[$1]+=$2} END {n = asorti(v, w); for (i = 1; i <= n; i++) print w[i],v[w[i]]}' motus_detected_genera`

salida esperada:
```
-1      0.0459829
Abiotrophia     1.30726e-05
Acidaminococcus 1.7287e-06
Acinetobacter   3.7644e-06
Actinobacteria  9.18046e-05
Actinomyces     0.000216577
Actinomycetaceae        3.2574e-06
Adlercreutzia   0.000154643
Aggregatibacter 7.711e-07
Agrobacterium   8.397e-07
...
```

----

1.10. Haz lo mismo que en 1.9, pero ordenando por abundancia de mayor a menor, para mostrar solamente los 10 géneros más abundantes en la muestra.

solución 1.10: `awk -F '|' 'function compare(i1,v1,i2,v2){return v2-v1} BEGIN{OFS="\t"}{v[$1]+=$2} END {n = asorti(v, w, "compare"); for (i = 1; i <= 10; i++) print w[i],v[w[i]]}' motus_detected_genera`

salida esperada:
```
Prevotella      0.2752
Faecalibacterium        0.212773
Bacteroides     0.0831857
Ruminococcus    0.069154
Clostridiales   0.0582455
Ruminococcaceae 0.0462725
-1      0.0459829
Bifidobacterium 0.0347672
Firmicutes      0.025413
[Eubacterium]   0.0221895
```


