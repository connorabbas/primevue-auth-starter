<script setup>
import InputError from "@/Components/InputError.vue";
import Toast from "primevue/toast";
import { useToast } from "primevue/usetoast";
import { useForm } from "@inertiajs/vue3";
import { ref } from "vue";

const currentPasswordInput = ref(null);
const passwordInput = ref(null);

const toast = useToast();
const form = useForm({
    current_password: "",
    password: "",
    password_confirmation: "",
});

const showSuccessToast = () => {
    toast.add({
        severity: "success",
        summary: "Saved",
        detail: "Your password has been updated",
        life: 3000,
    });
};
const updatePassword = () => {
    form.put(route("user-password.update"), {
        preserveScroll: true,
        onSuccess: () => {
            form.reset();
            showSuccessToast();
        },
        onError: () => {
            if (form.errors.updatePassword?.password) {
                form.reset("password", "password_confirmation");
                passwordInput.value.$el.focus();
            }
            if (form.errors.updatePassword?.current_password) {
                form.reset("current_password");
                currentPasswordInput.value.$el.focus();
            }
        },
    });
};
</script>

<template>
    <section>
        <header class="mb-5 flex">
            <div class="w-12 lg:w-10 xl:w-6">
                <h2 class="text-lg font-medium mt-0">Update Password</h2>

                <p class="mb-0 text-sm text-color-secondary">
                    Ensure your account is using a long, random password to stay
                    secure.
                </p>
            </div>
        </header>

        <Toast />

        <form @submit.prevent="updatePassword">
            <div class="mb-4 flex">
                <div class="w-12 lg:w-10 xl:w-6">
                    <label for="current_password" class="block mb-2"
                        >Current Password</label
                    >
                    <InputText
                        required
                        id="current_password"
                        ref="currentPasswordInput"
                        type="password"
                        v-model="form.current_password"
                        class="w-full"
                        :class="form.errors.updatePassword?.current_password ? 'p-invalid' : ''"
                        autocomplete="current-password"
                    />
                    <InputError
                        class="mt-2"
                        :message="form.errors.updatePassword?.current_password"
                    />
                </div>
            </div>

            <div class="mb-4 flex">
                <div class="w-12 lg:w-10 xl:w-6">
                    <label for="password" class="block mb-2"
                        >New Password</label
                    >
                    <InputText
                        required
                        id="password"
                        ref="passwordInput"
                        type="password"
                        v-model="form.password"
                        class="w-full"
                        :class="form.errors.updatePassword?.password ? 'p-invalid' : ''"
                        autocomplete="new-password"
                    />
                    <InputError
                        class="mt-2"
                        :message="form.errors.updatePassword?.password"
                    />
                </div>
            </div>

            <div class="mb-4 flex">
                <div class="w-12 lg:w-10 xl:w-6">
                    <label for="password_confirmation" class="block mb-2"
                        >Confirm Password</label
                    >
                    <InputText
                        required
                        id="password_confirmation"
                        type="password"
                        v-model="form.password_confirmation"
                        class="w-full"
                        :class="form.errors.updatePassword?.password_confirmation ? 'p-invalid' : ''"
                        autocomplete="new-password"
                    />
                    <InputError
                        class="mt-2"
                        :message="
                            form.errors.updatePassword?.password_confirmation
                        "
                    />
                </div>
            </div>

            <div class="flex align-content-center gap-3">
                <Button
                    raised
                    type="submit"
                    :loading="form.processing"
                    label="Save"
                    severity="contrast"
                />

                <Transition
                    enter-active-class="transition ease-in-out"
                    enter-from-class="opacity-0"
                    leave-active-class="transition ease-in-out"
                    leave-to-class="opacity-0"
                >
                    <p
                        v-if="form.recentlySuccessful"
                        class="text-sm text-gray-600 dark:text-gray-400"
                    >
                        Saved.
                    </p>
                </Transition>
            </div>
        </form>
    </section>
</template>
