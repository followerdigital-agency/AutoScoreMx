# Contexto para continuar AutoScoreMX en Claude Code

## Dónde estamos
- Landing (`index.html`) y las 9 pantallas del flujo (01-datos-auto, 02-informacion-adicional,
  03-pago, 03-5-analisis-en-proceso, 04-resultado, consultar-reporte, verificar-codigo,
  reporte-en-preparacion, reporte-encontrado) están construidas y desplegadas en Vercel,
  conectadas por links entre sí. Todo es HTML/CSS/JS estático, sin backend real todavía.
- Repo en GitHub: followerdigital-agency/AutoScoreMx, carpeta local: Documents/GitHub/AutoScoreMx
- Documento completo de lógica de negocio: `AutoScoreMX-Business-Logic.md` (en otro chat,
  pero se puede pedir de nuevo o reconstruir del contexto de este archivo)

## Backend: Supabase (ya aprovisionado)
- Project URL: https://nnlxcelrbkreffxotezo.supabase.co
- Publishable key: sb_publishable_WRLtbtqL5VDKbUhY-tzGEw_SkEbne5j
- Esquema SQL ya corrido (archivo `supabase-schema.sql`): tablas `vehicles`, `reports`,
  `report_findings`, `report_validation_points`, `vehicle_photos`, con RLS activado.
- Auth por correo (OTP, sin contraseña) configurado, con SMTP de Resend conectado.
- Plantilla de correo del código de verificación ya personalizada con diseño de marca.

## Lo que sigue (Fase B, en progreso)
Conectar de verdad estas 4 pantallas a Supabase (sin depender aún de la pasarela de pago):
1. `consultar-reporte.html` → `supabase.auth.signInWithOtp({ email })`
2. `verificar-codigo.html` → `supabase.auth.verifyOtp({ email, token, type: 'email' })`
3. `reporte-en-preparacion.html` / `reporte-encontrado.html` → tras verificar, consultar la
   tabla `reports` filtrada por el correo autenticado (RLS ya restringe a sus propios
   reportes), y decidir cuál de las dos pantallas mostrar según el campo `status`.
4. `04-resultado.html` → cargar datos reales de `report_findings` y
   `report_validation_points` en vez del contenido de ejemplo actual.

## Fases pendientes después de la B
- Fase C: conectar 01-datos-auto.html y 02-informacion-adicional.html (que las fotos se
  suban de verdad al bucket de Storage `vehicle-photos`, datos viajen entre pantallas)
- Fase D: elegir pasarela de pago (Stripe/Conekta/Mercado Pago — aún no decidido) y conectar
  03-pago.html: crear el reporte real en la base de datos, generar el checkout, y el
  webhook que confirma el pago y dispara el correo de "tu reporte está en proceso"

## Reglas de negocio importantes a mantener
- Las 5 categorías (fuente única de verdad): Historial, Identidad del vehículo,
  Precio de mercado, Análisis de imágenes, Inconsistencias
- El reporte se arma 100% manual por un analista (no hay motor automatizado aún) — el
  Table Editor de Supabase sirve como herramienta interna para que el analista llene
  `report_findings` y `report_validation_points` directamente
- Precio: $199 MXN, pago único, sin suscripción
- Nunca usar lenguaje de "garantizado", "certificado", "sin riesgo" — siempre "riesgo
  bajo/medio/alto", "basado en la información disponible", etc.
