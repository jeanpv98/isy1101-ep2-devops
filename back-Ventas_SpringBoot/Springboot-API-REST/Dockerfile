# ── Etapa 1: Compilar con Maven ──────────────────────────────────────
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app

# Copiar solo pom.xml primero para aprovechar caché de dependencias
COPY pom.xml .
RUN mvn dependency:go-offline -q

# Copiar código fuente y compilar
COPY src ./src
RUN mvn clean package -DskipTests

# ── Etapa 2: Imagen final mínima (mínimo privilegio) ─────────────────
FROM eclipse-temurin:17-jre-alpine

# Crear usuario no root (seguridad: mínimo privilegio)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Solo el JAR, sin el SDK de compilación (imagen mucho más pequeña)
COPY --from=build /app/target/*.jar app.jar

RUN chown appuser:appgroup app.jar

USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
