# Stage 1: Build the application
FROM maven:3.8.4-openjdk-17 as build

# Créer un utilisateur
RUN groupadd -r mygroup && useradd -r -g mygroup -m -d /home/myuser myuser
USER myuser
# Définition du répertoire de travail
WORKDIR /home/myuser/workspace/app
# Copier le fichier de configuration du projet (pom.xml) dans le répertoire de travail
COPY pom.xml .
# Copier le répertoire source dans le répertoire de travail
COPY src src
# Installer les dépendances Maven et construire l'application
RUN mvn clean install -DskipTests
# Créer un répertoire pour les dépendances et extraire le contenu du JAR
RUN mkdir -p target/dependency && (cd target/dependency ; jar -xf ../*.jar)
# Stage 2: Création de l'image finale avec l'intégration de MySQL
FROM openjdk:22-ea-22
# Définir un volume temporaire
VOLUME /tmp
# Spécifier le répertoire de dépendances
ARG DEPENDENCY=home/myuser/workspace/app/target/dependency
# Copier les bibliothèques nécessaires de la première étape
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app
# Copier le fichier de configuration application.properties
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes/application.properties /app/application.properties
# Ajouter MySQL JDBC driver
COPY --from=build /home/myuser/workspace/app/src/main/resources/mysql-connector-java-*.jar /app/lib/mysql-connector-java.jar
# Définir le point d'entrée de l'application
ENTRYPOINT ["java","-cp","app:app/lib/*", "com.bezkoder.spring.datajpa.SpringBootDataJpaApplication"]