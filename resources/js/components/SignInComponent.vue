<template>
    <div
        class="form-container
        sign-in-container"
    >
        <Form
            @submit="handleLogin"
            :validation-schema="schema"
        >
            <h1 class="mb-4">
                Sign in
            </h1>
            <Field
                name="email"
                type="text"
                class="form-input"
                placeholder="Email"
            />
            <ErrorMessage
                name="email"
                class="error-feedback"
            />
            <Field
                name="password"
                type="password"
                class="form-input"
                placeholder="Password"
                autocomplete="on"
            />
            <ErrorMessage
                name="password"
                class="error-feedback"
            />
            <button
                class="mt-2"
                :disabled="loading"
            >
                <span
                    v-show="loading"
                    class="spinner-border
                    spinner-border-sm"
                ></span>
                <span>Sign In</span>
            </button>
        </Form>
    </div>
</template>
<script>
import * as yup from "yup";
import {ErrorMessage, Field, Form} from "vee-validate";
import ToastService from "../services/toast-service";
export default {
    name: "SignInComponent",
    components: {
        Form,
        Field,
        ErrorMessage,
    },
    data() {
        const schema = yup.object().shape({
            email: yup.string().required("Email is required!"),
            password: yup.string().required("Password is required!"),
        });
        return {
            loading: false,
            message: "",
            test: null,
            schema,
        };
    },
    methods: {
        handleLogin(user) {
            this.loading = true;
            this.$store.dispatch("auth/login", user)
                .then((user) => {
                    this.$router.push("/")
                    ToastService.showToast(user.message, "success")
                },
                (error) => {
                    this.loading = false;
                    this.message =
                        (error.response &&
                            error.response.data &&
                            error.response.data.message) ||
                        error.message ||
                        error.toString();
                    ToastService.showToast(this.message, "error")
                }
            );
        },
        toogleSignInAndSignUp() {
            this.isSignIn = !this.isSignIn;
        }
    },
    computed: {
        loggedIn() {
            return this.$store.state.auth.status.loggedIn;
        },
    },
    created() {
        if (this.loggedIn) {
            this.$router.push("/login");
        }
    },
}
</script>

