STRUCT Product
    id: String
    name: String
    price: Decimal
    stock: Integer
    weight: Decimal
END

STRUCT CartItem
    product: Product
    quantity: Integer
END

STRUCT Cart
    items: List<CartItem>
    coupon: Coupon | null
    shippingAddress: Address | null
END

STRUCT Coupon
    code: String
    type: "percent" | "fixed"
    value: Decimal
    minSubtotal: Decimal
    expiresAt: Date
END

STRUCT Address
    name: String
    street: String
    city: String
    postalCode: String
    country: String
END
