# Ejercicios para AWK

Ejercicios para practicar awk

## 1

data/motus_profile

Este es un fichero procedente del análisis, de una muestra de secuenciación de DNA metagenómica, con mOTUs v2.5.1 https://github.com/motu-tool/mOTUs_v2.
Dados unos "reads", el programa los mapea contra una serie de genes marcadores, e intenta dar un perfil de especies presentes en la muestra.

El fichero tiene una cabecera (filas que comienzan por "#"), y luego filas de 4 campos.
Comencemos por ver la cabecera del fichero. Podemos hacer esto fácilmente con grep:

```
grep "^#" data/motus_profile
```

```
# git tag version 2.5.1 |  motus version 2.5.1 | map_tax 2.5.1 | gene database: nr2.5.1 | calc_mgc 2.5.1 -y insert.scaled_counts -l 50 | calc_motu 2.5.1 -k mOTU -C no_CAMI -g 1 -p -u | taxonomy: ref_mOTU_2.5.1 meta_mOTU_2.5.1
# call: python /home/cantalapiedra/miniconda3/bin/../share/motus-2.5.1//motus profile -f /data/jhc/prokaryokit/capture_clean_reads/16s001178_1P.fq -r /data/jhc/prokaryokit/capture_clean_reads/16s001178_2P.fq -s /data/jhc/prokaryokit/capture_clean_reads/16s001178_1U.fq -n 16s001178 -o motus2/capture/16s001178.motus_profile -I motus2/capture/16s001178.bam -M motus2/capture/16s001178.mgc -p -u -g 1 -l 50 -t 40
#mOTU   consensus_taxonomy      NCBI_tax_id     16s001178
```

¿Cómo hacemos esto mismo con awk?

solucion:
awk '/^#/' data/motus_profile

salida esperada:
la misma que para el grep

Deberiamos ver que el fichero tiene las columnas:
mOTU: un identificador para el grupo identificado.
consensus_taxonomy: la clasificación taxonómica consenso para dicho mOTU.
NCBI_tax_id: el identificador de grupo taxonómico en la base de datos NCBI Taxonomy.
16s001178: este es el nombre de la muestra analizada en este experimento en concreto. La columna contendrá la abundancia relativa de este mOTU en la muestra.


Ahora veamos los valores para la primera fila de datos. Podemos hacer esto fácilmente con:

grep -v "^#" data/motus_profile | head -1

¿Cómo hacemos esto mismo con awk?

solucion:
awk '/^#/ {next} {print $0; exit}' data/motus_profile

salida esperada:
la misma que para el grep