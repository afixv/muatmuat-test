<template>
  <div class="container mx-auto p-4 max-w-7xl">
    <NuxtLink to="/update" class="text-blue-500 hover:underline mb-4"
      >Back to Update</NuxtLink
    >
    <h1 class="text-2xl font-bold mb-4">
      {{
        selectedProduct
          ? `Edit Product: ${selectedProduct?.title}`
          : "Add New Product"
      }}
    </h1>
    <Loading v-if="loading && route.path === '/update/edit'" />
    <form onsubmit="return false" class="space-y-4">
      <div>
        <label for="title">Title</label>
        <input
          v-model="form.title"
          id="title"
          name="title"
          type="text"
          required
          placeholder="Enter product title" />
      </div>
      <div>
        <label for="price">Price</label>
        <input
          v-model="form.price"
          id="price"
          name="price"
          type="number"
          min="0"
          required
          placeholder="Enter product price" />
      </div>
      <div>
        <label for="stock">Stock</label>
        <input
          v-model="form.stock"
          id="stock"
          name="stock"
          type="number"
          min="0"
          required
          placeholder="Enter product stock" />
      </div>
      <button
        type="submit"
        @click="selectedProduct ? editData() : addData()"
        class="btn btn-primary">
        {{ selectedProduct ? "Edit Product" : "Add Product" }}
      </button>
    </form>
  </div>
</template>

<script lang="ts" setup>
import { useMakeTitleSlug } from "@/composables/useMakeTitleSlug";
import type { Product } from "~/types";

interface Form {
  title: string;
  price: number | null;
  stock: number | null;
}

const form = reactive<Form>({
  title: "",
  price: null,
  stock: null,
});

const toast = useToast();

const route = useRoute();
const slug = route.params.slug;

const selectedProduct = ref<Product | null>(null);

const loading = ref(true);

onMounted(() => {
  try {
    const products = localStorage.getItem("products");
    if (products) {
      const productArray = JSON.parse(products);
      const product = productArray.find((p: Product) => p.slug === slug);
      if (product) {
        form.title = product.title;
        form.price = product.price;
        form.stock = product.stock;
      }
      selectedProduct.value = product;
    }
  } catch (error) {
    toast.error({
      message: "Failed to load products. Please try again.",
    });
  } finally {
    loading.value = false;
  }
});

const validateForm = () => {
  if (
    !form.title ||
    (!form.price && form.price !== 0) ||
    (!form.stock && form.stock !== 0)
  ) {
    toast.error({ message: "All fields are required." });
    return false;
  }

  if (form.price < 0 || form.stock < 0) {
    toast.error({
      message: "Price and stock must be non-negative.",
    });
    return false;
  }

  return true;
};

const addData = () => {
  const slug = useMakeTitleSlug(form.title);
  const products = localStorage.getItem("products");
  const productArray = products ? JSON.parse(products) : [];

  const router = useRouter();

  if (!validateForm()) {
    return;
  }

  if (slug === productArray.find((p: Product) => p.slug === slug)?.slug) {
    toast.error({
      message: "Another product with this title already exists.",
    });
    return;
  }

  productArray.push({
    title: form.title,
    price: form.price,
    stock: form.stock,
    slug: slug,
  });

  try {
    localStorage.setItem("products", JSON.stringify(productArray));
  } catch (error) {
    toast.error({
      message: "Failed to add product. Please try again.",
    });
  } finally {
    toast.success({ message: "Product added successfully!" });
    router.push("/update");
  }
};

const editData = () => {
  const products = localStorage.getItem("products");
  const productArray = products ? JSON.parse(products) : [];

  const selectedIndex = productArray.findIndex((p: Product) => p.slug === slug);

  if (selectedIndex === -1) {
    toast.error({
      message: "Product not found.",
    });
    return;
  }

  if (!validateForm()) {
    return;
  }

  const newSlug = useMakeTitleSlug(form.title);

  const isDuplicateSlug = productArray.some(
    (p: Product, idx: number) => p.slug === newSlug && idx !== selectedIndex
  );

  if (isDuplicateSlug) {
    toast.error({
      message: "Another product with this title already exists.",
    });
    return;
  }

  const updateProduct = {
    ...productArray[selectedIndex],
    title: form.title,
    price: form.price,
    stock: form.stock,
    slug: newSlug,
  };

  productArray[selectedIndex] = updateProduct;

  const router = useRouter();

  try {
    localStorage.setItem("products", JSON.stringify(productArray));
  } catch (error) {
    toast.error({
      message: "Failed to update product. Please try again.",
    });
  } finally {
    toast.success({ message: "Product updated successfully!" });
    router.push("/update");
  }
};
</script>

<style></style>
