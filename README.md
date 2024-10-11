pub mod user_route_mock;

#[cfg(test)]
mod tests {
    use dotenv::dotenv;
    use crate::user::{get_userinfo, get_userinfo_from_file};

    use actix_web::{http::header::ContentType, test, App};

    #[actix_web::test]
    async fn mock_get_userinfo() {
        let app = test::init_service(App::new().service(get_userinfo)).await;
        let req = test::TestRequest::get()
            .uri("/userinfo/{id}")
            .param("id", "INFY5")
            .insert_header(ContentType::plaintext())
            .to_request();

        println!("\n req : {:?}", req);
        let resp = test::call_service(&app, req).await;

        println!("\n resp : {:?}", resp);
        assert!(resp.status().is_success());
    }

    #[actix_web::test]
    async fn mock_userinfo_from_file() {
        dotenv().ok();

        let app = test::init_service(App::new().service(get_userinfo_from_file)).await;
        let req = test::TestRequest::get()
            .uri("/userinfo/file/{id}")
            .param("id", "INFY5")
            .insert_header(ContentType::json())
            .to_request();

        println!("\n req : {:?}", req);
        let resp = test::call_service(&app, req).await;

        println!("\n resp : {:?}", resp);
        assert!(resp.status().is_success());
    }
}




[dependencies]
actix-web = { version = "4.9.0", features = ["cookies", "macros", "compress-gzip", "compress-brotli" ]}
dotenv = "0.15.0"
serde = { version = "1.0.210", features = ["derive"] }
serde_json = "1.0.128"


MOCK_DATA_FILE=foo.json

[{
    "id":"INFY5",
    "name":"Manjula M"
}]
