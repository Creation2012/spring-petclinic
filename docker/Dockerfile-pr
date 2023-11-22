FROM eclipse-temurin:17-jdk-alpine
VOLUME /tmp

ENV MAINCLASS="org.springframework.samples.petclinic.PetClinicApplication"
ARG DEPENDENCY=target/dependency

COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app

ENTRYPOINT java -cp app:app/lib/* ${MAINCLASS}
