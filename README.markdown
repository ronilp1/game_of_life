data "aws_route53_zone" "selected" {
  name         = "idap.np.au1.aws.anz.com."
  private_zone = true
}

resource "aws_route53_record" "validation" {
  for_each = {
    for dvo in module.aws_acm_certificate.validation_options : dvo.domain_name => {
      name   = dvo.resource_record_name
      record = dvo.resource_record_value
    }
  }

  zone_id = data.aws_route53_zone.selected.zone_id
  name    = each.value.name
  type    = "CNAME"
  records = [each.value.record]
  ttl     = 60
}
