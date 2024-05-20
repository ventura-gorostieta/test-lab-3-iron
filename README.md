# Ventura Gorostieta Valentin
# test-lab-3-iron
Create testing lab. Iron hack with d@f

# Laboratorio 3. Tecnicas de testing efectivas.

# Escenario 1 - User Authentication Tests

Original Test Case (Pseudocode):
```
TEST UserAuthentication
  ASSERT_TRUE(authenticate("validUser", "validPass"), "Should succeed with correct credentials")
  ASSERT_FALSE(authenticate("validUser", "wrongPass"), "Should fail with wrong credentials")
END TEST
```
## Test Case Analysis and Refinement
El escenario no describe si se requiere inicializar data para probar, así como tampoco se describe si una vez finalizado un test, se debe limpiar data, pero en caso de requerirse podemos hacer uso de setup y clean para dicho fin.

El método setup() se ejecuta antes de cada test, permitiendo inicializar cualquier recurso necesario. Esto es útil para evitar la duplicación de código de configuración en cada test.
El método cleanup() se ejecuta después de cada test, asegurando que cualquier recurso utilizado sea limpiado y no afecte a otros tests.

Claridad y Enfoque del Test. El test original no estaba claro, ya que se tenían aparentemente 2 casos probando en un mismo fragmento, por lo que no cumplia con una responsabilidad unica a nivel de test unit. No era legible el objetivo del test. Con el ajuste, ahora cada test se encuentra segmentado y cumpliendo una responsabilidad unica. Se usa el patrón given, when then para una mejor estructura del test.

Aislamiento de los Casos de Prueba: El test uni estaban aislados, como ya se mencionó, en un mismo bloque, se hacia una prueba exitosa y un expect fallido. Ahora cada caso de prueba se encuentra aislado uno de otro, por lo que en la ejecución, tanto objetos como respuestas, no influyen una de otra.

Documentación y Explicaciones Racionales: El pseudocodigo original no contaba con un contexto claro de pruebas, lo cual dificultaba entender que se iba a probar o el resultado a esperar de dicho test.  Con explicación detallada y apoyo del patron given, when and then, se puede leer mejor el test y comprender que y para que se desea ejecutar dicho test: 

```
import spock.lang.Specification

class UserAuthenticationTest extends Specification {

    def setup() {
        // Inicialización y preparacioón de configuración para test.
        
    }

    def cleanup() {
        //Limpiaza de configuración u objetos utilizados en la ejecución de un test
    }

    def "should succeed with correct credentials - happy páth"() {
        given: "a valid user and password"
        String username = "validUser"
        String password = "validPass"

        when: "the authenticate method is called"
        boolean result = authenticate(username, password)

        then: "authentication is successful"
        result == true
    }

    def "should fail with incorrect password - Error"() {
        given: "a valid user and incorrect password"
        String username = "validUser"
        String password = "wrongPass"

        when: "the authenticate method is called"
        boolean result = authenticate(username, password)

        then: "authentication fails"
        result == false
    }

    private boolean authenticate(String username, String password) {
        // Método auxiliar para simular una autenticación de usuario.
        return username == "validUser" && password == "validPass"
    }
}


```

# Escenario 2 - Data Processing Functions
Original Test Case (Pseudocode):
```
TEST DataProcessing
  DATA data = fetchData()
  TRY
    processData(data)
    ASSERT_TRUE(data.processedSuccessfully, "Data should be processed successfully")
  CATCH error
    ASSERT_EQUALS("Data processing error", error.message, "Should handle processing errors")
  END TRY
END TEST

```
Claridad y Enfoque del Test: En la funcionalidad inicial, tenía mezclado la lógica en el procesamiento de datos y el manejo de errores en el mismo test unit, por lo que no cumplia con una prueba asialada y unitaria. Se realiza una separación de los casos de prueba: exitoso y el de error donde se espera un tipo de exception adicional se estructura con patron given, when y then.

Aislamiento de los Casos de Prueba: Al no tener clara ua separación de los escenarios de prueba, los resultados de la prueba exitosa o la de error, podían influir en el resultado. Al tener ya separados los métodos de pruebas unitarias,  ahora la ejecución y resultado de uno y otro no influyen entre si. Se aplica un setup y clean para garantizar la correcta inicialización y limpieza de objetos en dado caso de ser necesario.

Documentación: No se contaba con una clara documentación de los test y escenarios a probar. Por lo que se agrega documentaición para cada bloque   given, when y then.
Exceptiones:  Se observó que en el bloque de pseudocodigo proporcionado, se hacía uso de un try-catch directo,  por lo que no era claro la excepción a validar. Se agrega un  thrown de spoock para verificar el tipo de exception lanzado.

```
import spock.lang.Specification
import spock.lang.Unroll

class DataProcessingTest extends Specification {

     def setup() {
        // Inicialización y preparacioón de configuración para test.
        
    }

    def cleanup() {
        //Limpiaza de configuración u objetos utilizados en la ejecución de un test
    }

    def "should process data successfully - happy path"() {
        given: "valid data to be processed"
        Data data = fetchData()

        when: "the data is processed"
        processData(data)

        then: "the data should be processed successfully"
        data.processedSuccessfully == true
    }

    def "should handle data processing errors - exception "() {
        given: "invalid data that will cause an error during processing"
        Data data = fetchDataWithError()

        when: "the data processing is attempted"
        processData(data)

        then: "an appropriate error message should be returned"
        def error = thrown(DataProcessingException)
        error.message == "Data processing error"
    }

    private Data fetchData() {
        //build mock valid data
        new Data(processedSuccessfully: false)
    }

    private Data fetchDataWithError() {
        // build invalid data,
        new Data(processedSuccessfully: false, valid: false)
    }

    private void processData(Data data) {
        // process  data mock for happy path or error
        if (!data.valid) {
            throw new DataProcessingException("Data processing error")
        }
        data.processedSuccessfully = true
    }

    static class Data {
        boolean processedSuccessfully
        boolean valid = true
    }

    static class DataProcessingException extends RuntimeException {
        DataProcessingException(String message) {
            super(message)
        }
    }
}
```

# Scenario 3: UI Responsiveness
Original Test Case (Pseudocode):

```
TEST UIResponsiveness
  UI_COMPONENT uiComponent = setupUIComponent(1024)
  ASSERT_TRUE(uiComponent.adjustsToScreenSize(1024), "UI should adjust to width of 1024 pixels")
END TEST
```

Claridad y Enfoque del Test: El bloque original proporcionado, no era claro, se mezclaba la prueba con la inicialización o preparación del test. Se realiza el refactor para pasar la inicialización al bloque setup, y se deja la acción de verificar el test con su responsabilidad unica.

Aislamiento de los Casos de Prueba: Como ya se menciono, la prueba no era clara  y tampoco hacía uso de algun patrón estructural para test, como lo es given, when and then. Por lo que se realiza el refactor correspondiente para un mejor aislamiento.

Documentación: Se agrega documención necesaria en cada bloque del test, lo que permite entender, sea legible y mantenible ya que se podrá entender el objetivo de la prueba.

```
import spock.lang.Specification

class UIResponsivenessTest extends Specification {

    UIComponent uiComponent

    def setup() {
        // Se utiliza el bloque de inicialización del test.
        uiComponent = setupUIComponent(1024)
    }

    def cleanup() {
        // Una vez se cumple el test, se limpia el componente probado.
        uiComponent = null
    }

    def "UI should adjust to screen width of 1024 pixels - happy path"() {
        given: "a UI component setup with a screen width of 1024 pixels"
        //en caso de inicializar o configurar componentes propios de este test, van aquí.

        when: "the UI component checks its responsiveness to the screen width"
        boolean adjusts = uiComponent.adjustsToScreenSize(1024)

        then: "the UI component should adjust to the width of 1024 pixels"
        adjusts == true
    }

    private UIComponent setupUIComponent(int width) {
        // Configura y retorna el componente UI
        new UIComponent(width)
    }

// clase mock de UI, para este ejemplo se deja aquí, en un caso real se puede mover a una clase TestUtils.
    static class UIComponent {
        int width

        UIComponent(int width) {
            this.width = width
        }

        boolean adjustsToScreenSize(int width) {
            return this.width == width
        }
    }
}
```

