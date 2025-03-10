package com.example.homerentals

import android.content.Context
import android.net.Uri
import android.os.Bundle
import android.util.Patterns
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.compose.rememberLauncherForActivityResult
import androidx.activity.result.PickVisualMediaRequest
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Settings
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.input.PasswordVisualTransformation
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.NavHostController
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import androidx.navigation.navigation
import coil.compose.rememberAsyncImagePainter
import com.example.homerentals.ui.theme.HomeRentalsTheme
import kotlinx.coroutines.delay
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.asStateFlow
import java.util.*

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            HomeRentalsTheme {
                HomeRentalsApp()
            }
        }
    }
}

@Composable
fun HomeRentalsApp() {
    val navController = rememberNavController()
    val context = LocalContext.current
    val sharedPreferences = remember { context.getSharedPreferences("user_prefs", Context.MODE_PRIVATE) }
    var isLoggedIn by remember { mutableStateOf(sharedPreferences.getBoolean("isLoggedIn", false)) }

    NavHost(
        navController = navController,
        startDestination = if (isLoggedIn) "main" else "auth"
    ) {
        navigation(startDestination = "login", route = "auth") {
            composable("login") {
                LoginScreen(
                    onLoginSuccess = {
                        sharedPreferences.edit().putBoolean("isLoggedIn", true).apply()
                        isLoggedIn = true
                        navController.navigate("main") { popUpTo("auth") { inclusive = true } }
                    },
                    onSignupNavigate = { navController.navigate("signup") }
                )
            }
            composable("signup") {
                SignupScreen {
                    sharedPreferences.edit().putBoolean("isLoggedIn", true).apply()
                    isLoggedIn = true
                    navController.navigate("main") { popUpTo("auth") { inclusive = true } }
                }
            }
        }

        navigation(startDestination = "home", route = "main") {
            composable("home") { HomeScreen(navController) }
            composable("settings") { SettingsScreen(navController) { isLoggedIn = false } }
            composable("addProperty") { AddPropertyScreen(navController) }
            composable("rentProperty") { RentPropertyScreen(navController) }
        }
    }
}

@Composable
fun LoginScreen(onLoginSuccess: () -> Unit, onSignupNavigate: () -> Unit) {
    var email by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }
    var isLoading by remember { mutableStateOf(false) }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    // Use this effect to handle login process
    LaunchedEffect(isLoading) {
        if (isLoading) {
            delay(1500)
            isLoading = false
            onLoginSuccess()
        }
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(32.dp),
        verticalArrangement = Arrangement.Center
    ) {
        Text("Welcome Back", style = MaterialTheme.typography.headlineMedium)
        Spacer(Modifier.height(24.dp))

        OutlinedTextField(
            value = email,
            onValueChange = { email = it },
            label = { Text("Email") },
            modifier = Modifier.fillMaxWidth(),
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Email)
        )

        Spacer(Modifier.height(16.dp))

        OutlinedTextField(
            value = password,
            onValueChange = { password = it },
            label = { Text("Password") },
            modifier = Modifier.fillMaxWidth(),
            visualTransformation = PasswordVisualTransformation(),
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password)
        )

        errorMessage?.let {
            Text(
                text = it,
                color = MaterialTheme.colorScheme.error,
                modifier = Modifier.padding(top = 8.dp)
            )
        }

        Spacer(Modifier.height(24.dp))

        Button(
            onClick = {
                when {
                    email.isEmpty() -> errorMessage = "Email required"
                    !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> errorMessage = "Invalid email format"
                    password.isEmpty() -> errorMessage = "Password required"
                    password.length < 6 -> errorMessage = "Password must be at least 6 characters"
                    else -> {
                        errorMessage = null
                        isLoading = true
                    }
                }
            },
            modifier = Modifier.fillMaxWidth()
        ) {
            if (isLoading) CircularProgressIndicator(modifier = Modifier.size(24.dp)) else Text("Sign In")
        }

        Spacer(Modifier.height(16.dp))

        TextButton(
            onClick = onSignupNavigate,
            modifier = Modifier.fillMaxWidth()
        ) {
            Text("Don't have an account? Sign Up")
        }
    }
}

@Composable
fun SignupScreen(onSignupSuccess: () -> Unit) {
    var email by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }
    var confirmPassword by remember { mutableStateOf("") }
    var isLoading by remember { mutableStateOf(false) }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    // Use this effect to handle signup process
    LaunchedEffect(isLoading) {
        if (isLoading) {
            delay(1500)
            isLoading = false
            onSignupSuccess()
        }
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(32.dp),
        verticalArrangement = Arrangement.Center
    ) {
        Text("Create Account", style = MaterialTheme.typography.headlineMedium)
        Spacer(Modifier.height(24.dp))

        OutlinedTextField(
            value = email,
            onValueChange = { email = it },
            label = { Text("Email") },
            modifier = Modifier.fillMaxWidth(),
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Email)
        )

        Spacer(Modifier.height(16.dp))

        OutlinedTextField(
            value = password,
            onValueChange = { password = it },
            label = { Text("Password") },
            modifier = Modifier.fillMaxWidth(),
            visualTransformation = PasswordVisualTransformation(),
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password)
        )

        Spacer(Modifier.height(16.dp))

        OutlinedTextField(
            value = confirmPassword,
            onValueChange = { confirmPassword = it },
            label = { Text("Confirm Password") },
            modifier = Modifier.fillMaxWidth(),
            visualTransformation = PasswordVisualTransformation(),
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password)
        )

        errorMessage?.let {
            Text(
                text = it,
                color = MaterialTheme.colorScheme.error,
                modifier = Modifier.padding(top = 8.dp)
            )
        }

        Spacer(Modifier.height(24.dp))

        Button(
            onClick = {
                when {
                    email.isEmpty() -> errorMessage = "Email required"
                    !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> errorMessage = "Invalid email format"
                    password.isEmpty() -> errorMessage = "Password required"
                    password.length < 6 -> errorMessage = "Password must be at least 6 characters"
                    confirmPassword.isEmpty() -> errorMessage = "Confirm password required"
                    password != confirmPassword -> errorMessage = "Passwords do not match"
                    else -> {
                        errorMessage = null
                        isLoading = true
                    }
                }
            },
            modifier = Modifier.fillMaxWidth()
        ) {
            if (isLoading) CircularProgressIndicator(modifier = Modifier.size(24.dp)) else Text("Sign Up")
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun HomeScreen(navController: NavHostController) {
    val viewModel: PropertyViewModel = viewModel()
    val properties by viewModel.propertiesFlow.collectAsState()

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Available Properties") },
                actions = {
                    IconButton(onClick = { navController.navigate("settings") }) {
                        Icon(Icons.Default.Settings, contentDescription = "Settings")
                    }
                }
            )
        },
        floatingActionButton = {
            FloatingActionButton(onClick = { navController.navigate("addProperty") }) {
                Icon(Icons.Default.Add, contentDescription = "Add Property")
            }
        }
    ) { padding ->
        LazyColumn(
            modifier = Modifier
                .padding(padding)
                .fillMaxSize()
        ) {
            if (properties.isEmpty()) {
                item {
                    Text(
                        text = "No properties available",
                        modifier = Modifier
                            .fillMaxWidth()
                            .padding(16.dp),
                        textAlign = TextAlign.Center
                    )
                }
            }

            items(properties) { property ->
                PropertyItem(property = property) {
                    navController.navigate("rentProperty")
                }
            }
        }
    }
}

@Composable
fun PropertyItem(property: Property, onRentClick: () -> Unit) {
    Card(
        modifier = Modifier
            .padding(8.dp)
            .fillMaxWidth()
            .clickable(onClick = onRentClick),
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            property.imageUrl?.let { uri ->
                Image(
                    painter = rememberAsyncImagePainter(uri),
                    contentDescription = null,
                    modifier = Modifier
                        .height(200.dp)
                        .fillMaxWidth()
                        .clip(RoundedCornerShape(8.dp)),
                    contentScale = ContentScale.Crop
                )
                Spacer(modifier = Modifier.height(8.dp))
            }

            Text(property.title, style = MaterialTheme.typography.headlineSmall)
            Text(property.description, style = MaterialTheme.typography.bodyMedium)
            Spacer(modifier = Modifier.height(8.dp))
            Text("$${property.price}/month", style = MaterialTheme.typography.labelLarge)
            Spacer(modifier = Modifier.height(8.dp))
            Button(onClick = onRentClick, modifier = Modifier.align(Alignment.End)) {
                Text("Rent Now")
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun AddPropertyScreen(navController: NavHostController) {
    val viewModel: PropertyViewModel = viewModel()
    var title by remember { mutableStateOf("") }
    var description by remember { mutableStateOf("") }
    var price by remember { mutableStateOf("") }
    var imageUri by remember { mutableStateOf<Uri?>(null) }
    var loading by remember { mutableStateOf(false) }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    val imagePicker = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.PickVisualMedia(),
        onResult = { uri -> imageUri = uri }
    )

    // Use this effect to handle property addition
    LaunchedEffect(loading) {
        if (loading) {
            delay(1500)
            loading = false
            viewModel.addProperty(Property(title = title, description = description, price = price.toDouble(), imageUrl = imageUri))
            navController.popBackStack()
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Add New Property") }
            )
        }
    ) { padding ->
        Column(
            modifier = Modifier
                .padding(padding)
                .padding(16.dp)
                .fillMaxSize()
        ) {
            OutlinedTextField(
                value = title,
                onValueChange = { title = it },
                label = { Text("Title") },
                modifier = Modifier.fillMaxWidth(),
                isError = errorMessage?.contains("Title") == true
            )

            Spacer(modifier = Modifier.height(8.dp))

            OutlinedTextField(
                value = description,
                onValueChange = { description = it },
                label = { Text("Description") },
                modifier = Modifier.fillMaxWidth(),
                isError = errorMessage?.contains("Description") == true
            )

            Spacer(modifier = Modifier.height(8.dp))

            OutlinedTextField(
                value = price,
                onValueChange = { price = it },
                label = { Text("Price per month") },
                modifier = Modifier.fillMaxWidth(),
                keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
                isError = errorMessage?.contains("Price") == true
            )

            Spacer(modifier = Modifier.height(16.dp))

            Button(
                onClick = {
                    imagePicker.launch(PickVisualMediaRequest(ActivityResultContracts.PickVisualMedia.ImageOnly))
                },
                modifier = Modifier.fillMaxWidth()
            ) {
                Text("Select Property Image")
            }

            Spacer(modifier = Modifier.height(8.dp))

            imageUri?.let { uri ->
                Image(
                    painter = rememberAsyncImagePainter(uri),
                    contentDescription = null,
                    modifier = Modifier
                        .size(150.dp)
                        .clip(RoundedCornerShape(8.dp)),
                    contentScale = ContentScale.Crop
                )
            }

            errorMessage?.let {
                Text(
                    text = it,
                    color = MaterialTheme.colorScheme.error,
                    modifier = Modifier.padding(vertical = 8.dp)
                )
            }

            Spacer(modifier = Modifier.height(16.dp))

            Button(
                onClick = {
                    when {
                        title.isEmpty() -> errorMessage = "Title is required"
                        description.isEmpty() -> errorMessage = "Description is required"
                        price.isEmpty() -> errorMessage = "Price is required"
                        !price.matches(Regex("^\\d+(\\.\\d{1,2})?$")) -> errorMessage = "Invalid price format"
                        imageUri == null -> errorMessage = "Property image is required"
                        else -> {
                            errorMessage = null
                            loading = true
                        }
                    }
                },
                modifier = Modifier.fillMaxWidth()
            ) {
                if (loading) CircularProgressIndicator(modifier = Modifier.size(24.dp)) else Text("Add Property")
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun RentPropertyScreen(navController: NavHostController) {
    var loading by remember { mutableStateOf(false) }
    var success by remember { mutableStateOf(false) }

    // Use this effect to handle rental confirmation
    LaunchedEffect(loading) {
        if (loading) {
            delay(1500)
            loading = false
            success = true
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(if (success) "Rental Confirmed" else "Confirm Rental") }
            )
        }
    ) { padding ->
        Column(
            modifier = Modifier
                .padding(padding)
                .padding(16.dp)
                .fillMaxSize(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center
        ) {
            if (success) {
                Text("Rental Successful!", style = MaterialTheme.typography.headlineMedium)
                Spacer(modifier = Modifier.height(8.dp))
                Text("Your rental agreement has been confirmed")
                Spacer(modifier = Modifier.height(16.dp))
                Button(onClick = { navController.popBackStack() }) {
                    Text("Return to Home")
                }
            } else {
                Text("Confirm your rental details?", style = MaterialTheme.typography.bodyLarge)
                Spacer(modifier = Modifier.height(32.dp))

                if (loading) {
                    CircularProgressIndicator()
                } else {
                    Button(
                        onClick = { loading = true },
                        modifier = Modifier.width(200.dp)
                    ) {
                        Text("Confirm Rent")
                    }
                }
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun SettingsScreen(navController: NavHostController, onLogout: () -> Unit) {
    val context = LocalContext.current
    val sharedPreferences = remember { context.getSharedPreferences("user_prefs", Context.MODE_PRIVATE) }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Settings") }
            )
        }
    ) { padding ->
        Column(
            modifier = Modifier
                .padding(padding)
                .padding(16.dp)
                .fillMaxSize(),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Button(
                onClick = {
                    sharedPreferences.edit().putBoolean("isLoggedIn", false).apply()
                    onLogout()
                    navController.navigate("auth") {
                        popUpTo("main") { inclusive = true }
                    }
                },
                modifier = Modifier.width(200.dp)
            ) {
                Text("Log Out")
            }
        }
    }
}

data class Property(
    val id: String = UUID.randomUUID().toString(),
    val title: String,
    val description: String,
    val price: Double,
    val imageUrl: Uri? = null
)

class PropertyViewModel : ViewModel() {
    private val _propertiesFlow = MutableStateFlow<List<Property>>(emptyList())
    val propertiesFlow = _propertiesFlow.asStateFlow()

    fun addProperty(property: Property) {
        val currentList = _propertiesFlow.value.toMutableList()
        currentList.add(property)
        _propertiesFlow.value = currentList
    }
}
