# RedVital — Modelo de Datos

Modelo de datos organizado según las 5 épicas del backlog priorizado V1. Cada épica es dueña de sus propias entidades (un microservicio = una base de datos); las referencias cruzadas (`donante_id`, `banco_id`, `territorio_id`, etc.) son IDs lógicos, no llaves foráneas físicas entre bases distintas.

```
E1 Gestión de Donantes → E2 Trazabilidad y Ciclo de Vida → E3 Inventario y Alertas
                                                          ↘
                                            E4 Administración Territorial (transversal)
                                                          ↘
                                            E5 Gestión de Campañas (incluye gamificación)
```

---

## E1 — Gestión de Donantes
*Registro, elegibilidad e historial*

### Donante
| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| documento_identidad | string | único |
| nombre | string | |
| fecha_nacimiento | date | |
| genero | enum | |
| tipo_sangre | enum(A+,A-,B+,B-,AB+,AB-,O+,O-) | null hasta 1ª donación confirmada |
| email / telefono | string | |
| fecha_registro | datetime | |
| estado | enum(activo,inactivo,suspendido) | suspendido = no elegible temporalmente |

### CriterioElegibilidad
Evaluado en cada intento de donación (no es tabla maestra, es registro histórico de evaluaciones).

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| donante_id | UUID FK | |
| fecha_evaluacion | datetime | |
| edad_valida | bool | 18-65 años típico |
| peso_valido | bool | ≥50kg típico |
| intervalo_valido | bool | ≥56 días desde última donación (sangre completa) |
| hemoglobina | decimal | |
| resultado | enum(apto,no_apto,apto_con_reserva) | |
| motivo_no_apto | string nullable | |

### HistorialDonante (vista derivada, no tabla propia)
Agrega desde E2 y E5: `total_donaciones`, `ultima_donacion`, `componentes_donados[]`, `insignias[]`.

---

## E2 — Trazabilidad y Ciclo de Vida
*Origen, estados, disposición final y auditoría*

### Donacion
| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| donante_id | UUID FK | |
| campaña_id | UUID nullable FK | referencia a E5 |
| fecha | datetime | |
| lugar_id | UUID | |
| tipo_donacion | enum(sangre_completa,plaquetas,plasma,doble_globulo) | |
| volumen_ml | int | |
| estado | enum(registrada,en_tamizaje,aprobada,rechazada) | |

### PruebaLaboratorio
| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| donacion_id | UUID FK | |
| tipo_prueba | enum(VIH,hepatitis_B,hepatitis_C,sifilis,chagas,HTLV) | |
| resultado | enum(reactivo,no_reactivo,pendiente) | |
| fecha | datetime | |

### UnidadSangre
El origen: nace de una `Donacion` aprobada con todas las pruebas `no_reactivo`.

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | código de trazabilidad único (impreso en etiqueta física) |
| donacion_id | UUID FK | **origen** |
| tipo_sangre | enum | |
| componente | enum(globulos_rojos,plasma,plaquetas,crioprecipitado) | |
| volumen_ml | int | |
| fecha_extraccion | datetime | |
| fecha_vencimiento | date | calculada según vida útil del componente (ver E3) |
| estado | enum(en_cuarentena,disponible,reservada,transfundida,vencida,descartada) | **estados** |
| banco_id | UUID FK | referencia a E3/E4 |

**Máquina de estados:**
```
en_cuarentena → disponible → reservada → transfundida
disponible → vencida → descartada
cualquier estado → descartada (si falla control de calidad, con motivo)
```

### AsignacionUnidad
(id, solicitud_id, unidad_id FK, fecha_asignacion, compatibilidad_verificada:bool, verificado_por)

### Transfusion
Parte de la disposición final.

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| unidad_id | UUID FK | |
| paciente_id | UUID FK | |
| hospital_id | UUID FK | referencia a E4 |
| fecha_hora_inicio / fin | datetime | |
| personal_medico_id | UUID | |
| reaccion_adversa | bool | |
| tipo_reaccion | enum nullable(fiebre,alergica,hemolitica,otra) | |
| estado | enum(en_curso,completada,suspendida) | |

### DescarteUnidad
La otra disposición final posible.
(id, unidad_id FK, fecha_descarte, motivo enum(vencida,contaminada,transporte_defectuoso,otro), responsable_id)

### RegistroAuditoria
Traza cada cambio de estado de una unidad — le da sustento real a la palabra "auditoría".

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| unidad_id | UUID FK | |
| estado_anterior | enum | |
| estado_nuevo | enum | |
| fecha_hora | datetime | |
| usuario_id / sistema | string | quién/qué disparó el cambio (persona o job automático) |
| motivo | string nullable | |

---

## E3 — Inventario y Alertas
*Existencias, escasez y vencimientos*

### BancoSangre
(id, nombre, territorio_id FK → E4, capacidad_unidades)

### LoteAlmacenamiento
(unidad_id FK 1:1, banco_id FK, temperatura_almacenamiento, fecha_ingreso, ubicacion_fisica_rack)

### VidaUtilComponente (tabla de referencia estática)
| componente | dias_vida_util | condicion |
|---|---|---|
| globulos_rojos | 42 | refrigerado 1-6°C |
| plaquetas | 5 | agitación continua |
| plasma | 365 | congelado |
| crioprecipitado | 365 | congelado |

### UmbralMinimoStock
Define qué es "escasez" por banco.
(id, banco_id FK, tipo_sangre, componente, cantidad_minima)

### AlertaEscasez
(id, banco_id, tipo_sangre, componente, cantidad_actual, cantidad_minima, fecha_generada, atendida:bool)

### AlertaVencimiento
(id, unidad_id FK, fecha_alerta, dias_restantes, nivel(informativa,critica), notificado:bool)

---

## E4 — Administración Territorial
*Control de acceso por jurisdicción — aplica a bancos de sangre y hospitales*

### Territorio
| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| nombre | string | ej. "Bogotá D.C.", "Antioquia" |
| nivel | enum(pais,departamento,ciudad) | jerárquico |
| territorio_padre_id | UUID nullable FK | auto-referencia para jerarquía |

### Hospital
(id, nombre, territorio_id FK, direccion, nivel_complejidad)

`BancoSangre.territorio_id` (definido en E3) referencia esta entidad.

### UsuarioSistema
(id, nombre, email, rol enum(admin_nacional,admin_territorial,operador_banco,operador_hospital,medico))

### AccesoTerritorial
(id, usuario_id FK, territorio_id FK, rol_en_territorio, fecha_asignacion)

Un `admin_territorial` solo ve/gestiona bancos y hospitales dentro de su `territorio_id` (y territorios hijos, si aplica jerarquía).

---

## E5 — Gestión de Campañas
*Creación y administración de campañas, puntos, insignias y recompensas*

### Campaña
| Campo | Tipo | Notas |
|---|---|---|
| id | UUID (PK) | |
| nombre / descripcion | string | |
| fecha_inicio / fecha_fin | date | |
| territorio_id | UUID FK → E4 | dónde aplica |
| meta_unidades | int | |
| unidades_recolectadas | int | derivado, vía evento donation.completed |
| puntos_por_donacion | int | multiplicador de la campaña |
| tipo_sangre_objetivo | enum nullable | para campañas dirigidas por escasez |
| estado | enum(planificada,activa,cerrada) | |

### ParticipacionCampaña
(donante_id FK → E1, campaña_id FK, fecha, puntos_ganados)

### Insignia
(id, nombre, descripcion, criterio_regla, icono_url)
Ej. criterio_regla: `{"tipo": "donaciones_consecutivas", "valor": 5}`

### DonanteInsignia
(donante_id FK, insignia_id FK, fecha_obtenida)

### PuntosDonante
Se mantiene en E5 (no en E1) porque es resultado de la gamificación.
(donante_id FK, puntos_totales, nivel enum(bronce,plata,oro,platino), fecha_actualizacion)

### Recompensa
(id, nombre, costo_puntos, stock, activo)

### Canje
(id, donante_id FK, recompensa_id FK, fecha, puntos_utilizados, estado enum(pendiente,entregado,cancelado))

---

## Trazabilidad extremo a extremo

```
E1 Donante ──(elegible)──> E2 Donacion ──> UnidadSangre[en_cuarentena→disponible]
                                                    │
                              E3 Inventario/Alertas ┤ (vencimiento, escasez)
                              E4 Territorio ─────────┤ (autoriza acceso por banco/hospital)
                                                    │
                                          reserva → E2 Transfusion (disposición final)
                                                    │
                              E5 Campañas/Gamificación ← evento donation.completed
```
