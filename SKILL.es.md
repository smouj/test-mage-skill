name: Test Mage
description: Una habilidad especializada que conjura suites de pruebas completas desde cero, automatizando la creación de pruebas unitarias, de integración y end-to-end para varios lenguajes de programación y frameworks.
tags: testing, automation, coding, magic
version: 1.0.0
author: SMOUJ
dependencies: pytest>=7.0.0, jest>=29.0.0, mocha>=10.0.0, selenium>=4.0.0, cypress>=12.0.0
environment_variables:
  - TEST_MAGE_API_KEY: Clave API requerida para generación de datos de prueba externos (opcional para modo local)
  - TEST_MAGE_MODE: Establecer en 'offline' para generación solo local sin dependencias externas
  - TEST_MAGE_TIMEOUT: Tiempo máximo en segundos para generación de pruebas (predeterminado: 300)
requirements:
  - Node.js >= 16.0.0 o Python >= 3.8
  - Repositorio Git con código fuente
  - package.json o requirements.txt existente para análisis de dependencias
verification_steps:
  - Ejecutar 'test-mage verify' para verificar la configuración del entorno
  - Asegurar que no haya conflictos con archivos de prueba existentes
  - Hacer respaldo del directorio de pruebas actual antes de la ejecución
---

## Propósito

Test Mage está diseñado para eliminar el tedioso trabajo manual de escribir suites de pruebas al generar mágicamente pruebas de alta calidad y completas basadas en análisis de código. Analiza el código fuente, identifica funciones, clases y endpoints, y crea casos de prueba que cubren casos límite, manejo de errores y escenarios de integración.

### Casos de Uso Reales

- **Prototipado Rápido**: Al desarrollar una nueva funcionalidad en una aplicación React, Test Mage genera pruebas Jest para componentes, hooks y llamadas a API, asegurando que las interacciones de UI y cambios de estado estén cubiertos sin escribir un solo archivo de prueba.
- **Refactorización de Código Legacy**: Para una API Flask de Python con funciones no documentadas, Test Mage escanea el código base y produce suites de pruebas pytest que validan el comportamiento existente, previniendo regresiones durante la modernización.
- **Mejora de Pipeline CI/CD**: En un microservicio Node.js, Test Mage crea pruebas end-to-end Cypress para flujos críticos de usuario como login, checkout y envío de datos, integrándose perfectamente en GitHub Actions para pruebas automatizadas.
- **Pruebas Multiidioma**: Para un proyecto poliglota con frontend JavaScript y backend Python, Test Mage genera suites de pruebas coincidentes para ambos, asegurando que los contratos de API se prueben end-to-end.

## Alcance

Test Mage opera con comandos específicos adaptados a diferentes escenarios de pruebas. Soporta los siguientes comandos CLI, cada uno con flags para personalización:

- `test-mage conjure <source-dir> --framework=<jest|pytest|mocha> --type=<unit|integration|e2e> --coverage=<80-100>`: Genera pruebas para el directorio especificado usando el framework elegido y tipo de prueba, apuntando a un porcentaje mínimo de cobertura.
- `test-mage analyze <file-path> --output=<json|yaml> --depth=<shallow|deep>`: Analiza un archivo o módulo único, produciendo un desglose estructurado de componentes testeables en formato JSON o YAML.
- `test-mage integrate --ci=<github|gitlab|jenkins> --branch=<main|feature-branch>`: Agrega pruebas generadas a pipelines CI, configurando runners y triggers para la rama especificada.
- `test-mage mock <api-endpoint> --method=<GET|POST> --response=<status-code>`: Crea servicios mock para dependencias externas, simulando respuestas de API para pruebas aisladas.
- `test-mage verify <test-dir> --run=<true|false> --report=<html|json>`: Valida pruebas generadas ejecutándolas y produciendo informes detallados.

El alcance excluye escritura manual de pruebas, benchmarking de rendimiento (usar herramientas separadas) y despliegue en producción.

## Proceso de Trabajo

Test Mage sigue un proceso estructurado e iterativo para asegurar generación confiable de pruebas:

1. **Escaneo de Código**: Analiza el directorio fuente usando parsing AST para identificar funciones, clases, importaciones y dependencias. Por ejemplo, en un proyecto TypeScript, detecta componentes React y sus props.

2. **Creación de Plano de Pruebas**: Genera un plano JSON que describe casos de prueba, incluyendo escenarios positivos/negativos, mocks y aserciones. Este paso considera patrones de pruebas existentes en el código base.

3. **Adaptación de Framework**: Mapea el plano al framework de pruebas especificado (ej., convertir a bloques Jest describe/it o fixtures pytest).

4. **Inyección de Casos Límite**: Agrega automáticamente pruebas para entradas nulas, valores límite y manejo de excepciones basados en hints de tipo o JSDoc.

5. **Configuración de Integración**: Si se solicita, configura runners de pruebas (ej., agregar configuración Cypress para pruebas e2e) e inyecta en scripts CI.

6. **Ejecución de Validación**: Ejecuta un subconjunto de pruebas para verificar que pasan contra el código actual, marcando cualquier fallo inmediato.

7. **Salida Final**: Escribe archivos de prueba en un directorio designado (predeterminado: `tests/generated/`), con convenciones de nomenclatura claras como `test-component.spec.js`.

Las dependencias se instalan automáticamente si faltan, y las variables de entorno se verifican al inicio.

## Reglas de Oro

- **Sin Sobrescritura Sin Respaldo**: Nunca modificar archivos de prueba existentes; siempre generar nuevos en directorios aislados para prevenir pérdida de datos.
- **Umbrales de Cobertura**: Aplicar cobertura mínima del 85% para pruebas unitarias; advertir si está por debajo, pero no fallar la generación.
- **Paridad de Lenguaje**: Mantener calidad de pruebas consistente entre lenguajes—ej., pruebas Python usan fixtures similares a beforeEach de Jest.
- **Aislamiento Primero**: Priorizar pruebas unitarias sobre integración; generar mocks para llamadas externas para evitar pruebas inestables.
- **Fijación de Versiones**: Usar versiones específicas de framework (ej., jest@29.5.0) para asegurar reproducibilidad.
- **Transparencia de Errores**: Registrar todas las decisiones de generación en un archivo `test-mage.log`, incluyendo por qué ciertas pruebas fueron omitidas.
- **Modo Incremental**: Al re-ejecutar en código actualizado, comparar con planos previos para agregar solo pruebas nuevas, evitando duplicación.

## Ejemplos

### Ejemplo 1: Generación de Pruebas Unitarias para un Componente React
**Entrada del Usuario**: `test-mage conjure src/components/Button --framework=jest --type=unit --coverage=90`

**Salida**:
- Genera `tests/generated/Button.spec.js` con pruebas para variaciones de props, manejadores de click y atributos de accesibilidad.
- Código Generado de Muestra:
  ```javascript
  describe('Button Component', () => {
    it('renders with default props', () => {
      render(<Button />);
      expect(screen.getByRole('button')).toBeInTheDocument();
    });
    it('handles onClick callback', () => {
      const mockOnClick = jest.fn();
      render(<Button onClick={mockOnClick} />);
      fireEvent.click(screen.getByRole('button'));
      expect(mockOnClick).toHaveBeenCalledTimes(1);
    });
  });
  ```
- Verificación: Ejecutar `npm test tests/generated/Button.spec.js` para asegurar 90% de cobertura.

### Ejemplo 2: Pruebas End-to-End para un Endpoint de API
**Entrada del Usuario**: `test-mage conjure routes/auth.js --framework=cypress --type=e2e --coverage=95`

**Salida**:
- Crea `tests/generated/auth.cy.js` con pruebas de flujo de login.
- Código Generado de Muestra:
  ```javascript
  describe('Authentication Flow', () => {
    it('logs in successfully', () => {
      cy.visit('/login');
      cy.get('[data-cy=email]').type('test@example.com');
      cy.get('[data-cy=password]').type('password123');
      cy.get('[data-cy=submit]').click();
      cy.url().should('include', '/dashboard');
    });
  });
  ```
- Solución de Problemas: Si las pruebas fallan debido a problemas de red, usar `test-mage mock /api/login --method=POST --response=200` para crear mocks.

### Ejemplo 3: Análisis de un Módulo Python
**Entrada del Usuario**: `test-mage analyze utils/calculator.py --output=json --depth=deep`

**Salida**:
- Archivo JSON detallando funciones como `add(a, b)`, `divide(a, b)`, con sugerencias de casos de prueba.
- Fragmento de Salida de Muestra:
  ```json
  {
    \"functions\": [
      {
        \"name\": \"add\",
        \"params\": [\"a\", \"b\"],
        \"test_cases\": [
          {\"input\": [1, 2], \"expected\": 3},
          {\"input\": [-1, 1], \"expected\": 0}
        ]
      }
    ]
  }
  ```

## Comandos de Rollback

- `test-mage rollback <test-dir>`: Elimina todos los archivos de prueba generados en el directorio especificado, restaurando desde un respaldo pre-generación.
- `test-mage undo --commit=<hash>`: Revierte el último commit si incluía pruebas generadas, usando `git reset --hard <hash>`.
- `test-mage clean --framework=<jest|pytest>`: Elimina configuraciones y dependencias específicas del framework agregadas durante la generación.
- `test-mage reset`: Limpia todos los caches y logs, devolviendo Test Mage a un estado limpio para ejecuciones frescas.