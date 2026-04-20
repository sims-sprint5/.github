# Chatbot SIMS - Documentación Completa

## Resumen Ejecutivo

**Chatbot IA** integrado en SIMS que proporciona soporte automático a usuarios y administradores mediante **Groq AI**. Responde preguntas sobre reservas, vehículos, problemas técnicos y funcionalidades del sistema.

- **API:** Groq (mixtral-8x7b-32768)
- **Tiempo respuesta:** ~1-2 segundos
- **Autenticación:** Requerida (Bearer token Sanctum)
- **Costo:** Gratis (free tier Groq)

---

## ¿Qué Hace el Chatbot?

### Sí Puede:
- Responder preguntas sobre reservas ("¿Cómo modifico mi reserva?")
- Explicar cómo funcionan los vehículos
- Guiar a usuarios en funcionalidades del sistema
- Dar contexto personalizado basado en datos del usuario
- Resolver dudas sobre disponibilidad
- Explicar estados de reservas y cancelaciones
- Responder en tiempo real

### No Puede:
- Crear reservas directamente (solo guía al usuario)
- Cambiar datos del servidor (solo lee contexto)
- Acceder a información de otros usuarios
- Procesar pagos o transacciones

---

## Endpoints

### POST /api/v1/chat/ask

**Envía una pregunta al chatbot y recibe respuesta.**

#### Headers Requeridos:
```
Authorization: Bearer {access_token}
Content-Type: application/json
```

#### Body (Request):
```json
{
  "message": "¿Cómo modifico la fecha de mi reserva?"
}
```

#### Response (200 OK):
```json
{
  "message": "Para modificar tu reserva:\n\n1. Ve a 'Mis Reservas'\n2. Haz clic en la reserva que quieres modificar\n3. Presiona 'Editar Fecha'\n4. Selecciona la nueva fecha de fin\n5. El sistema verifica disponibilidad\n6. Confirma los cambios\n\nEl cambio se aplica inmediatamente si la nueva fecha está disponible.",
  "timestamp": "2026-04-20T14:32:15Z"
}
```

#### Response (401 Unauthorized):
```json
{
  "message": "Unauthenticated."
}
```
**Solución:** Envía un token válido en el header Authorization

#### Response (500 Server Error):
```json
{
  "error": "Error al conectar con Groq API"
}
```
**Solución:** Verifica que GROQ_API_KEY está configurada en .env

---

## Cómo Usar desde Vue.js

### 1. Componente Básico

```vue
<template>
  <div class="chat-container">
    <div class="messages">
      <div v-for="msg in messages" :key="msg.id" :class="msg.role">
        {{ msg.content }}
      </div>
    </div>
    
    <input 
      v-model="userMessage" 
      @keyup.enter="ask"
      placeholder="Haz tu pregunta..."
    />
    <button @click="ask">Enviar</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      messages: [],
      userMessage: '',
      token: localStorage.getItem('access_token'), // Ajusta según tu setup
    };
  },
  methods: {
    async ask() {
      if (!this.userMessage.trim()) return;

      // Agregar pregunta a la UI
      this.messages.push({
        id: Date.now(),
        role: 'user',
        content: this.userMessage,
      });

      const question = this.userMessage;
      this.userMessage = '';

      try {
        // Construir contexto (opcional pero recomendado)
        const context = `
USUARIO: ${this.currentUser?.name || 'Usuario'}
PÁGINA: ${this.$route.name || 'inicio'}
RESERVAS ACTIVAS: ${this.reservations?.length || 0}
        `.trim();

        const enrichedMessage = `${context}\n\nPREGUNTA: ${question}`;

        // Enviar al chatbot
        const response = await fetch('/api/v1/chat/ask', {
          method: 'POST',
          headers: {
            'Authorization': `Bearer ${this.token}`,
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            message: enrichedMessage,
          }),
        });

        if (response.ok) {
          const data = await response.json();
          this.messages.push({
            id: Date.now() + 1,
            role: 'assistant',
            content: data.message,
          });
        } else if (response.status === 401) {
          this.messages.push({
            id: Date.now() + 1,
            role: 'error',
            content: 'No autenticado. Por favor inicia sesión.',
          });
        } else {
          const error = await response.json();
          this.messages.push({
            id: Date.now() + 1,
            role: 'error',
            content: `Error: ${error.error || 'Error desconocido'}`,
          });
        }
      } catch (err) {
        this.messages.push({
          id: Date.now() + 1,
          role: 'error',
          content: `Error de conexión: ${err.message}`,
        });
      }
    },
  },
};
</script>
```

### 2. Componente Completo con Contexto (Recomendado)

Ver: `/docs/chatbot-groq.md` (incluye componente Vue completo con CSS, manejo de contexto dinámico, etc.)

---

## Contexto que Recibe la IA

El chatbot puede recibir contexto sobre:

```javascript
// Información del usuario
{
  nombre: "Juan García",
  email: "juan@example.com",
  rol: "Usuario", // o "Admin"
}

// Reservas activas
{
  reservas: [
    {
      id: 1,
      vehiculo: "Tesla Model 3",
      del: "2026-04-20 10:00",
      al: "2026-04-25 18:00",
      estado: "pending"
    }
  ]
}

// Vehículo que está mirando
{
  vehiculo: "Tesla Model 3",
  placa: "ABC-123",
  disponible: true,
  estado: "available"
}

// Página actual
{
  pagina: "mapa" // o "mis-reservas", "tickets", etc
}
```

**Ventaja:** Permite respuestas personalizadas y específicas al contexto del usuario.

---

## Ejemplos de Uso

### Ejemplo 1: Pregunta Simple
```
Usuario: "¿Cómo cancelo una reserva?"

IA: "Para cancelar tu reserva:

1. Ve a 'Mis Reservas'
2. Encuentra la reserva que quieres cancelar
3. Si el estado es 'Pendiente' y aún no ha empezado, verás botón 'Cancelar'
4. Haz clic y confirma
5. La reserva quedará marcada como cancelada

Las reservas que ya iniciaron no pueden ser canceladas por usuarios normales.
Si es urgente, contacta a soporte."
```

### Ejemplo 2: Pregunta Contextual
```
Contexto enviado:
- Usuario: María López
- Reserva activa: BMW X5 (20-25 abril, PENDING)
- Página: mis-reservas

Usuario: "¿Puedo extender mi reserva?"

IA: "Sí María, puedes extender tu reserva del BMW X5 (20-25 abril):

1. Haz clic en la tarjeta de la reserva
2. Presiona 'Modificar Fecha'
3. Cambia la fecha de fin a 26 de abril
4. El sistema verifica disponibilidad automáticamente
5. Si está disponible, confirma

Como tu reserva está en estado 'Pendiente', puedes cambiar la fecha sin problema.
Si quieres extender más días, verifica disponibilidad en el mapa primero."
```

### Ejemplo 3: Pregunta sobre Vehículo
```
Contexto enviado:
- Vehículo mirando: Tesla Model 3 (ABC-123)
- Disponibilidad: available
- Página: mapa

Usuario: "¿Este coche es bueno para familias?"

IA: "Sí, el Tesla Model 3 es excelente para familias:

- Capacidad: 5 pasajeros + maletero grande
- Comodidad: Asientos cómodos, aire acondicionado
- Seguridad: Últimas tecnologías de seguridad
- Eficiencia: No necesita gasolina, muy económico
- Silencio: Motor eléctrico, muy silencioso

Actualmente está DISPONIBLE. ¿Quieres hacer una reserva?"
```

---

## Seguridad

| Característica | Implementación |
|---|---|
| **Autenticación** | Token Sanctum (verificado en cada request) |
| **API Key Groq** | Almacenada en .env (nunca en código) |
| **Datos sensibles** | No se envía información privada a Groq |
| **Usuario aislado** | Cada usuario solo ve su contexto |
| **Rate limiting** | Configurable según necesidad |

---

## Sistema de Prompts

El ChatController envía a Groq con este formato:

```
SISTEMA:
[Prompt del sistema con instrucciones sobre SIMS]

CONTEXTO:
- Proyecto: SIMS - Sistema de Gestión de Reservas
- Usuario: [nombre]
- Rol: [Usuario/Admin]
- Página: [mapa/mis-reservas/etc]
- Contexto adicional: [si aplica]

PREGUNTA:
[Pregunta del usuario]
```

**Ventaja:** La IA siempre entiende el contexto del proyecto y da respuestas alineadas.

---

## Deployment

### Prerrequisitos:
1.  `GROQ_API_KEY` configurada en `.env`
2.  `GROQ_MODEL=mixtral-8x7b-32768` configurada
3.  Usuario autenticado en la aplicación
4.  Token válido de Sanctum

### En Producción:
```bash
# Verificar que .env tiene Groq configurado
grep GROQ_ .env

# Reiniciar servicios si es necesario
docker-compose restart sims_api
```

---

## Troubleshooting

| Problema | Causa | Solución |
|---|---|---|
| "Unauthenticated" | Token no enviado | Verifica qué nombre usa tu app para guardar el token (access_token, token, etc) |
| "Error al conectar con Groq" | API key inválida/no existe | Verifica GROQ_API_KEY en .env |
| Respuesta vacía | Timeout Groq | Reintentar (es extraordinario, Groq es rápido) |
| 404 en /api/v1/chat/ask | Ruta no registrada | Verifica que ChatController esté importado en routes/tenant.php |
| Respuesta genérica | Contexto no enviado | Incluye contexto en el mensaje para respuestas personalizadas |

---

## Monitoreo

Para ver qué está pasando con las requests:

```bash
# En tiempo real, desde el servidor
tail -f storage/logs/laravel.log | grep -i chat

# O buscar en archivo de logs
grep -i "chat" storage/logs/laravel.log
```

---

## Casos de Uso

### Para Usuarios Normales:
- "¿Cuáles son los pasos para reservar?"
- "¿Puedo cambiar mi fecha?"
- "¿Qué significa 'Pendiente'?"
- "¿Cómo cancelo?"
- "¿Es seguro este vehículo?"

### Para Administradores:
- "¿Cómo gestiono reservas de usuarios?"
- "¿Cómo marco un vehículo en mantenimiento?"
- "¿Cómo veo el historial de reservas?"
- "¿Cómo asigno tickets?"

---

## Notas Importantes

1. **Token Obligatorio:** Sin token válido, obtendrás 401
2. **Contexto Inteligente:** Incluye contexto para respuestas personalizadas
3. **Respuestas Formateadas:** La IA devuelve Markdown, puedes usar en UI
4. **Tiempo Real:** ~1-2 segundos por respuesta
5. **Multilingual:** La IA puede responder en varios idiomas (envía en ese idioma)
6. **Escalable:** Groq maneja muchas requests sin problema


