using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using iTextSharp.text;
using iTextSharp.text.pdf;

class Program
{
    static void Main()
    {
        HashSet<string> ciudadanos = new HashSet<string>();
        HashSet<string> vacunadosPfizer = new HashSet<string>();
        HashSet<string> vacunadosAstrazeneca = new HashSet<string>();
        HashSet<string> vacunadosDosDosis = new HashSet<string>();

        // Generar 500 ciudadanos
        for (int i = 1; i <= 500; i++)
        {
            ciudadanos.Add($"Ciudadano {i}");
        }

        // Generar 75 ciudadanos vacunados con Pfizer
        Random rnd = new Random();
        while (vacunadosPfizer.Count < 75)
        {
            int num = rnd.Next(1, 501);
            vacunadosPfizer.Add($"Ciudadano {num}");
        }

        // Generar 75 ciudadanos vacunados con AstraZeneca
        while (vacunadosAstrazeneca.Count < 75)
        {
            int num = rnd.Next(1, 501);
            if (!vacunadosPfizer.Contains($"Ciudadano {num}"))
            {
                vacunadosAstrazeneca.Add($"Ciudadano {num}");
            }
        }

        // Ciudadanos que recibieron ambas dosis
        vacunadosDosDosis = new HashSet<string>(vacunadosPfizer.Intersect(vacunadosAstrazeneca));

        // Ciudadanos no vacunados
        HashSet<string> noVacunados = new HashSet<string>(ciudadanos.Except(vacunadosPfizer).Except(vacunadosAstrazeneca));

        // Ciudadanos que solo recibieron Pfizer
        HashSet<string> soloPfizer = new HashSet<string>(vacunadosPfizer.Except(vacunadosDosDosis));

        // Ciudadanos que solo recibieron AstraZeneca
        HashSet<string> soloAstrazeneca = new HashSet<string>(vacunadosAstrazeneca.Except(vacunadosDosDosis));

        // Generar el PDF con los resultados
        CrearReportePDF(noVacunados, vacunadosDosDosis, soloPfizer, soloAstrazeneca);
    }

    static void CrearReportePDF(HashSet<string> noVacunados, HashSet<string> vacunadosDosDosis, HashSet<string> soloPfizer, HashSet<string> soloAstrazeneca)
    {
        Document doc = new Document();
        PdfWriter.GetInstance(doc, new FileStream("ReporteVacunacion.pdf", FileMode.Create));
        doc.Open();

        doc.Add(new Paragraph("Reporte de Vacunación COVID-19"));
        doc.Add(new Paragraph("\nListado de ciudadanos no vacunados:"));
        doc.Add(new Paragraph(string.Join(", ", noVacunados)));

        doc.Add(new Paragraph("\nListado de ciudadanos con dos dosis:"));
        doc.Add(new Paragraph(string.Join(", ", vacunadosDosDosis)));

        doc.Add(new Paragraph("\nListado de ciudadanos que solo recibieron Pfizer:"));
        doc.Add(new Paragraph(string.Join(", ", soloPfizer)));

        doc.Add(new Paragraph("\nListado de ciudadanos que solo recibieron AstraZeneca:"));
        doc.Add(new Paragraph(string.Join(", ", soloAstrazeneca)));

        doc.Close();
    }
}
