package cs1302.api;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.scene.control.Label;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.VBox;
import javafx.scene.Scene;
import javafx.stage.Stage;


import javafx.scene.control.TextField;
import javafx.scene.control.Button;
import javafx.scene.control.ComboBox;
import javafx.scene.control.ListView;
import javafx.scene.layout.HBox;



/**
 * This is an app that takes recipe from MealDB and user chooses from list after choosing country.
 * Then when they click it they click the nutrition button to get
 * more information about the food itself which mealdb doesnt provide.
 * The nutrition info comes from edamam api.
 */
public class ApiApp extends Application {
    Stage stage;
    Scene scene;
    VBox root;

    ComboBox<String> cuisineBox;
    ListView<MealDbApi.MealSummary> resultsList;
    Label detailsLabel;
    Button searchButton;
    Button nutritionButton;
    VBox detailsBox;

    MealDbApi mealApi;
    MealDbApi.MealDetail selectedMeal;


    /**
     * Constructs an {@code ApiApp} object. This default (i.e., no argument)
     * constructor is executed in Step 2 of the JavaFX Application Life-Cycle.
     */
    public ApiApp() {
        root = new VBox();
    } // ApiApp

    /** {@inheritDoc} */
    @Override
    public void start(Stage stage) {
        this.stage = stage;


        // setup scene


        initControls();
        buildLayout();
        initHandlers();


        scene = new Scene(root);



        stage.setTitle("ApiApp!");
        stage.setScene(scene);
        stage.setOnCloseRequest(event -> Platform.exit());
        stage.sizeToScene();
        stage.show();

    } // start

    /**
     * method for the controls specifically choosing the type of cusine.
     */
    private void initControls() {
        mealApi = new MealDbApi();
        selectedMeal = null;
        cuisineBox = new ComboBox<>();
        cuisineBox.getItems().addAll( "american",  "Argentinian", "British",
            "Canadian", "Chinese", "chinese", "Croatian",
            "Dutch", "Egyptian", "French", "Greek", "Indian", "Irish",
            "Italian", "Jamaican", "Japanese", "Kenyan", "Malaysian"
            , "Mexican", "Moroccan", "Polish","Portuguese","Russian","Spanish"
            ,"Thai", "Tunisian", "Turkish", "Vietnamese ");

        cuisineBox.setPromptText("Choose a cusine");

        searchButton = new Button("Find Meals");

        resultsList = new ListView<>();
        resultsList.setPrefHeight(300);

        detailsLabel = new Label("Select meal to view details");
        detailsBox = new VBox(5,detailsLabel);

        nutritionButton = new Button("Get nutrition info");
        nutritionButton.setDisable(true);




    }

    /**
     * method that controls buttons and gets meal.
     */
    private void initHandlers() {

        searchButton.setOnAction(e -> {

            String cuisine = cuisineBox.getValue();

            if (cuisine == null) {
                detailsLabel.setText("Please select a cuisine first.");
                return;
            }

            MealDbApi.MealSummary[] meals = mealApi.getMealsByArea(cuisine);

            resultsList.getItems().clear();
            if (meals.length == 0) {
                detailsLabel.setText("No meals found for " + cuisine + ".");
            } else {
                resultsList.getItems().addAll(meals);
                detailsLabel.setText("Select a meal to view details.");
            }
            nutritionButton.setDisable(true);
            selectedMeal = null;


        });

        resultsList.getSelectionModel().selectedItemProperty().addListener((obs, oldV, newV) -> {
            if (newV != null) {
                MealDbApi.MealDetail detail = mealApi.getMealDetails(newV.idMeal);
                selectedMeal = detail;
                if (detail != null) {
                    detailsLabel.setText(
                        "Meal: " + detail.strMeal + "\n" +
                        "Area: " + detail.strArea + "\n" +
                        "Category: " + detail.strCategory + "\n\n" +
                        "Instructions:\n" + detail.strInstructions
                    );
                    nutritionButton.setDisable(false);
                } else {
                    detailsLabel.setText("Error loading meal details.");
                    showAlert("Could not load choose another meal");
                    nutritionButton.setDisable(true);
                }
            }
        });

        nutritionButton.setOnAction(e -> {
            if (selectedMeal != null) {
                showNutritionWindow(selectedMeal);

            }

        });


    }
    
    
      /**
     * opens a new window for nutrition.
     * @param meal this takes in the meal the user chose and displays info.
     * close after.
     */
    private void showNutritionWindow(MealDbApi.MealDetail meal) {
        Stage nutStage = new Stage();
        VBox nutRoot = new VBox(10);
        Label title = new Label("Nutrition Breakdown");
        title.setStyle("-fx-font-size: 20px; -fx-font-weight: bold;");
        ListView<String> nutList = new ListView<>();
        try {
            NutritionApi nApi = new NutritionApi();
            var ingredientLines = meal.toIngredientLines();
            if (ingredientLines.isEmpty()) {
                nutList.getItems().add("No ingredients found for this meal.");
            } else {
                String[] ingredientsArray = ingredientLines.toArray(new String[0]);
                NutritionResponse n = nApi.analyzeIngredients(ingredientsArray);

                if (n == null) {
                    nutList.getItems().add("Error retrieving nutrition data.");
                } else {
                    // TOTAL NUTRITION
                    nutList.getItems().add("=== TOTAL NUTRITION FOR RECIPE ===");
                    nutList.getItems().add("Calories: " + n.calories);
                    nutList.getItems().add("Fat: " + n.getNutrient("FAT") + " g");
                    nutList.getItems().add("Carbs: " + n.getNutrient("CHOCDF") + " g");
                    nutList.getItems().add("Protein: " + n.getNutrient("PROCNT") + " g");
                    nutList.getItems().add("");
                }
            }
        } catch (Exception ex) {
            
            nutList.getItems().add("Error retrieving nutrition info.Choose one not as complicated");
            ex.printStackTrace();
            showAlert("Recipe too complicated choose another one.");
        }
        
        Button closeBtn = new Button("Close");
        closeBtn.setOnAction(e -> nutStage.close());
        nutRoot.getChildren().addAll(title, nutList, closeBtn);
        Scene nutScene = new Scene(nutRoot, 500, 550);
        nutStage.setTitle("Nutrition Details");
        nutStage.setScene(nutScene);
        nutStage.show();
    }
    
    /**
     * Helper to show alerts with a message.
     * @param message the message to be shown.
     */
    private void showAlert(String message) {
        javafx.scene.control.Alert alert =
            new javafx.scene.control.Alert(javafx.scene.control.Alert.AlertType.ERROR);
        alert.setTitle("Error");
        alert.setHeaderText("Something went wrong");
        alert.setContentText(message);
        alert.showAndWait();
    }

    







    /**
     * builds actual layout.
     */
    private void buildLayout() {
        HBox searchRow = new HBox(10,cuisineBox, searchButton);
        root.getChildren().addAll(searchRow, resultsList, detailsBox, nutritionButton);
    }





} // ApiApp
